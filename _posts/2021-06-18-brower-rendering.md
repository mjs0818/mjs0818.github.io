---
layout: single
title: "브라우저 렌더링 과정"
---

렌더링이란 HTML, CSS, 자바스크립트로 작성된 문서를 파싱하여 브라우저에 시각적으로 출력하는 것을 말한다.

웹 어플리케이션의 자바스크립트는 브라우저에서 HTML, CSS와 함께 실행이 되는데 브라우저 렌더링 과정을 이해하면 좀 더 효율적인 자바스크립트 프로그래밍이 가능할 것이다. 이를 위해 브라우저가 HTML, CSS, 자바스크립트로 작성된 문서를 어떻게 파싱하여 브라우저에 렌더링하는지 살펴보자.

브라우저는 다음과 같은 과정을 거쳐 렌더린을 수행한다.
1. HTML, CSS, 자바스크립트, 이미지, 폰트 파일 등 렌더링에 필요한 리소스를 서버로 요청하고 응답을 받는다.
2. 브라우저의 렌더링 엔진은 서버로부터 받은 HTML과 CSS를 파싱하여 DOM, CSSOM을 생성하고 이들을 결합하여 렌더 트리를 생성한다.
3. 브라우저의 자바스크립트 엔진은 서버로부터 받은 자바스크립트를 파싱하여 AST(Abstract Syntax Tree)를 생성하고 바이트코드로 변환하여 실행한다. 이때 자바스크립트는 DOM API를 통해 DOM이나 CCSOM을 변경할 수 있고 변경된 DOM과 CSSOM은 다시 렌더 트리로 결한된다.
4. 렌더 트리를 기반으로 HTML 요소의 레이아웃(위치, 크기)를 계산하고 브라우저 화면에 HTML 요소를 페인팅한다.


# 요청과 응답

브라우저의 핵심 기능은 필요한 리소스를 서버에 요청하고 서버로부터 응답받아 브라우저에 시각적으로 렌더링하는 것이다.

서버에 요청을 전송하기 위해 브라우저의 주소창을 제공한다. 주소창에 URL을 입력하면 URL의 도메인이 IP 주소로 변환되고 해당 주소를 갖는 서버에 요청을 전송한다.

예를 들어 주소창에 https://google.com 을 입력하면 해당 서버로 요청을 전송하고 명확한 리소스를 요청하진 않지만 서버는 루트 요청에 대해 암묵적으로 index.html을 응답하도록 기본 설정되어 있다. 
즉, https://google.com 은 https://google.com/index.html 과 같은 요청이다.

만약 index.html이 아닌 다른 정적 파일을 요청하려면 요청할 정적 파일의 경로를 입력하여 서버에 요청할 수 있다.

<br>

![스크린샷, 2021-06-18 01-02-34](https://user-images.githubusercontent.com/67500926/122433274-0b527580-cfd1-11eb-9089-14410e034346.png)

요청과 응답은 개발자 도구의 Network 탭에서 확인할 수 있는데 네트워크 탭을 살펴보면 index.html 뿐만 아니라 CSS, 자바스크립트, 이미지, 폰트 등의 파일도 응답한 것을 확인할 수 있다.

이는 브라우저의 렌더링 엔진이 HTML 파싱 도중 외부 리소르를 로드하는 태그, 즉 CSS 파일을 로드하는 link 태그, 이미지 파일을 로드하는 img 태그, 자바스크립트를 로드하는 script 태그 등을 만나면 HTML의 파싱을 중단하고 해당 파일을 서버로 요청하기 때문이다.


# HTML, CSS 파싱

## HTML 파싱과 DOM 생성
서버에서 전달받은 HTML 문서는 문자열로 이루어진 순수한 텍스트다. 순수한 텍스트인 HTML 문서를 브라우저에 렌더링하려면 HTML 문서를 브라우저가 이해할 수 있는 객체로 변환해야한다.

예를 들어, 서버로 부터 다음과 같은 index.html을 받았다고 해보자.
```html
 <!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet">
    <title>Critical Path</title>
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
  </body>
</html> 
```

브라우저의 렌더링 엔진은 다음과 같은 과정을 통해 브라우저가 이해할 수 있는 객체인 DOM을 생성한다.

![image](https://user-images.githubusercontent.com/67500926/122435787-42298b00-cfd3-11eb-9327-1c3883ba168c.png)


1. 브라우저는 HTML 문서를 바이트(2진수) 형태로 응답받는다. 해당 파일에 지정된 인코딩 방식(예: UTF-8)에 따라 문자로 변환한다.

2. 문자열을 읽어 들여 문법적 의미를 갖는 코드의 최소 단위인 토큰으로 분해한다.

3. 각 토큰들을 객체로 변환하여 노드를 생성한다. 노드는 DOM을 구성하는 기본요소가 된다.
```js
{
    startTag: 'html'
    contents: {
        startTag: 'head',
        contents: { ... },
        ...
    }
    endTag: 'html'
}
```

4. 마지막으로, HTML 요소는 중첩 관계를 갖기 때문에 트리 자료구조로 구성한다. 

## CSS 파싱과 CSSOM 생성
렌더링 엔진은 HTML을 한 줄씩 순차적으로 파싱하며 DOM을 생성해 나간다. 그러다가 CSS를 로드하는 link 태그나 style 태그를 만나면 DOM 생성을 중단하고 즉시 리소스를 요청한다. 

서버에 요청하여 로드한 CSS 파일이나 style 태그 내의 CSS를 HTML과 동일한 파싱 과정(바이트 -> 문자 -> 토큰 -> 노드 -> CSSOM)을 거쳐 CSSOM을 생성한다. CSS 파싱을 완료하면 다시 HTML 파싱을 시작하여 DOM 생성을 재개한다.

```css
 body { font-size: 16px }
 p { font-weight: bold }
 span { color: red }
 p span { display: none }
 img { float: right } 
```

![image](https://user-images.githubusercontent.com/67500926/122438093-5078a680-cfd5-11eb-9828-4cb65207b31d.png)

CSSOM은 CSS의 상속을 반영하여 생성된다. 위 코드에서 body에 적용한 font-size는 모든 하위 요소에 상속되며 p 태크에 적용한 font-weight도 하위의 span 요소에 상속된다.


## 렌더 트리 생성

HTML과 CSS를 파싱하여 각각 DOM과 CSSOM을 생성하고 렌더 트리로 결합된다.

렌더 트리는 렌더링을 위한 트리 자료 구조로 브라우저 화면에 렌더링되지 않는 노드(meta 태그, script 태그, diplay: none 등)는 포함하지 않는다.

![image](https://user-images.githubusercontent.com/67500926/122439120-528f3500-cfd6-11eb-9f16-56ab7c048b24.png)

이후 완성된 렌더 트리는 각 HTML 요소의 레이아웃(위치와 크기)을 계산하는데 사용되며 마지막으로 렌더링 트리의 각 노드를 실제 픽셀로 변환되는데 이 단계를 페인팅이라고 한다.


# 자바스크립트 파싱과 실행
자바스크립트는 동적으로 DOM과 CSSOM을 조작할 수 있는 DOM API를 제공한다. 
CSS 파싱과 마찬가지로 DOM을 생성해 나가다 script 태그를 만나면 DOM 생성을 일시 중단한다.

script 태그에 의해 자바스크립트 파일을 서버에 요청하여 로드한 자바스크립트 코드를 파싱하기 위해 자바스크립트 엔진에 제어권을 넘긴다. 

자바스크립트 엔진은 렌더링 엔진이 DOM과 CSSOM을 생성하듯이 자바스크립트를 해석하여 AST(추상 구문 트리)를 생성한다. AST를 기반으로 인터프리터가 실행할 수 있는 중간 코드인 바이트코드를 생성하여 실행한다.

## 리플로우와 리페인트
자바스크립트 코드에 DOM이나 CSSOM을 변경하는 DOM API가 사용된 경우 DOM이나 CSSOM이 변경된다. 이때 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합되고 변경된 렌더 트리를 기반으로 레이아웃과 페인팅 과정을 거쳐 브라우저의 화면에 다시 렌더링한다. 이를 리플로우, 리페인트라 한다.

리플로우는 레이아웃 계산을 다시 하는 것을 말하며, 노드 추가/삭제, 요소의 크기/위치 변경, 윈도우 리사이징 등 레이아웃에 영향을 주는 변경이 발생한 경우에 실행된다. 

리페인트는 재결합된 렌더 트리를 기반으로 다시 페인팅 하는 것을 말한다.

따라서 리플로우와 리페인트가 반드시 동시에 실행되는 것은 아니다. 레이아웃에 영향이 없는 변경은 리플로우 없이 리페인트만 실행된다.

## 자바스크립트 파싱에 의한 HTML 파싱 중단
브라우저는 동기적으로 위에서 아래 방향으로 순차적으로 HTML, CSS, 자바스크립트를 파싱하고 실행한다. 이것은 script 태그의 위치에 따라 HTML 파싱이 블로킹되어 DOM 생성이 지연될 수 있음을 의미한다.

또한, 자바스크립트는 DOM API를 통해 DOM이나 CSSOM을 변경할 수 있다. 만약 DOM API를 사용할 때 DOM이나 CSSOM이 아직 생성되지 않았다면 문제가 발생할 수 있다.

이러한 문제를 회피하기 위해 body 요소의 가장 아래에 script 태그를 위치시키며 다음과 같은 이점이 있다.
- DOM이 완성되지 않은 상태에서 자바스크립트가 DOM을 조작하는 에러가 발생할 우려가 없다
- 자바스크립트 로딩/파싱/실행으로 인해 HTML 요소들의 렌더링에 지장받는 일이 없어 페이지 로딩 시간이 단축된다.

## script 태그의 async / defer

앞서 살펴본 자바스크립트 파싱에 의한 DOM 생성이 중단되는 문제를 해결하기 위해 HTML5부터 script 태그에 async와 defer 어트리뷰트가 추가되었다. 

async와 defer는 src를 통해 외부 자바스크립트 파일을 로드하는 경우에만 사용할 수 있다. 즉 인라인 자바스크립트에는 사용할 수 없다.


```html
<script async src="extern.js"></script>
<script defer src="extern.js"></script>
```

async와 defer를 사용하면 HTML 파싱과 자바스크립트 파일의 로드가 비동기적으로 진행된다. 하지만 자바스크립트의 실행 시점에 차이가 있다.

### **async**
async는 자바스크립트의 파싱과 실행은 파일이 로드된 직후 진행되며 이때 HTML 파싱이 중단된다. 
또한 여러개의 script 태그에 async를 지정하면 태그의 순서와 상관없이 로드가 완료된 자바스크립트 먼저 실행되므로 순서가 보장되지 않는다. 순서 보장이 필요한 script 태그에는 async를 지정하지 않아야 한다.


### **defer**
defer는 자바스크립트 파일을 로드한 후 HTML 파싱이 완료된 후에 파싱과 실행이 진행된다. 따라서 DOM 생성이 완료된 이후 실행되어야 할 자바스크립트에 유용하다.

# Reference
- [객체 모델 생성](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/constructing-the-object-model?hl=ko)

- [렌더링 트리 생성, 레이아웃 및 페인트](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=ko)

- 모던 자바스크립트 Deep Dive