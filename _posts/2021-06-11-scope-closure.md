---
layout: single
title: "[Javascript] 스코프와 클로저의 개념"
---

클로저(Closure)라는 개념은 자바스크립트 동작을 이해하기 위해 알아야하는 개념이다. 클로져를 이해하기 위해서 스코프에 대한 이해가 필요하다.

스코프(Scope)는 자바스크립트 뿐만 아니라 모든 프로그래밍 언어에서 가장 기본적이고 중요한 개념이다. 먼저, 스코프가 무엇인지 정리하고 클로져에 대해 알아보자.

<br>

# **스코프(Scope)란?**

스코프는 변수, 함수, 클래스 이름같은 식별자가 선언된 위치에 따라 자신이 참조될 수 있는지 없는지 범위가 결정되는데 이런 `유효범위`를 스코프라고 한다. 다음 코드를 살펴보자.

```js
function foo() {
  var x = 1;
  console.log(x);
}

foo();

console.log(x);
```

이 코드를 보면 함수 foo가 실행되면서 console.log(x)에서 1이 출력되고 마지막 console.log(x)에서는 참조 에러가 발생한다라는 것은 알고 있을 것이다. 여기에 스코프라는 개념을 적용해보면 변수 x는 foo라는 함수에서 선언이 되었기때문에 foo 함수 내부에서만 참조될 수 있는 스코프를 가진다. 따라서 foo 함수 내부에서 실행한 console.log(x)는 정상적으로 실행되지만 함수 밖에서 x를 참조하는 console.log(x)는 에러가 발생하는 것이다.

<br>

## 스코프 종류

스코프는 참조될 수 있는 유효범위라고 했는데 이 유효범위는 `전역 스코프`와 `지역 스코프` 크게 두 가지로 나뉜다. <br>
코드 전체는 전역과 지역으로 구분할 수 있는데 전역에서 선언된다면 전역 스코프를 가지고 지역에서 선언된다면 지역 스코프를 가지게 된다.

<br>

```js
// 전역 스코프
var x = "전역변수 x";
var y = "전역변수 y";
function outer() {
  // outer 함수 지역 스코프
  var y = "outer의 지역변수 y";
  console.log(x); // 전역변수 x
  console.log(y); // outer의 지역변수 y

  function inner() {
    // inner 함수 지역 스코프
    var x = "inner의 지역변수 x";
    console.log(x); // inner의 지역변수 x
    console.log(y); // outer의 지역변수 y
  }

  inner();
}

outer();
console.log(x); // 전역변수 x
console.log(y); // 전역변수 y
```

전역 스코프를 가지는 식별자는 코드 전체에서 참조될 수 있고, 지역 스코프를 가지는 식별자는 선언된 함수나 블록 내에서만 참조될 수 있다.

하지만 위처럼 전역과 지역에 동일한 변수가 선언되어 있을 수 있다. 이런 상황에서 자바스크립트 엔진은 어떻게 올바른 변수를 참조할 수 있을까? 아무 변수나 막 참조하지 않을 것이다. 그래서 올바른 변수를 참조하기 위한 스코프 체인에 대해서 알아보겠다.

<br>

## 스코프 체인

스코프 체인이란 스코프가 계층적으로 연결된 것을 말한다.
앞서 확인한 코드를 살펴 보면 전역에 outer함수가 선언되었고 outer함수 내부에 inner함수가 선언되어 있다. 따라서 inner함수의 상위 스코프는 outer함수의 스코프가 되고 outer함수의 상위 스코프는 전역 스코프가 된다.

이처럼 스코프는 계층적으로 연결이 되고 자바스크립트 엔진은 스코프 체인을 따라 변수를 참조하게 된다.
현재 스코프 안에 원하는 변수가 없다면 상위 스코프로 올라가며 변수를 찾고 전역 스코프까지 올라가서도 변수가 없다면 Reference Error를 출력한다.

변수를 찾을 때는 상위 스코프로만 이동하기 때문에 하위 스코프에서는 상위 스코프 변수를 참조할 수 있게 된다.

<br>

## 스코프 레벨

스코프는 어떤 레벨을 가지는가에 따라 블록 레벨 스코프와 함수 레벨 스코프로 구분할 수 있다.

### 함수 레벨 스코프

자바스크립트는 `var` 키워드로 선언된 변수나 `함수 선언식`으로 만들어진 함수는 오로지 함수 코드 블록만을 지역 스코프로 가진다. 이런 특성을 `함수 레벨 스코프`라고 한다.
함수 레벨 스코프는 함수 코드 블록에서 선언된 모든 식별자는 함수 내에서 유효한 식별자가 된다.

```js
function foo() {
  if (true) {
    var x = "함수 레벨 스코프";
  }
  console.log(x); // 함수 레벨 스코프
}
```

`var x`가 블록 레벨 스코프를 가졌다면 if 블럭을 벗어난 console.log(x)는 참조 에러가 발생할 것이다.
하지만 x는 함수 레벨 스코프를 가지기 때문에 foo 함수 내부 어디서든 호출하더라도 정상적으로 참조할 수 있다.

### 블록 레벨 스코프

대부분의 프로그래밍 언어는 함수와 더불어 if문, for문과 같은 모든 코드 블록이 지역 스코프를 만든다. 이런 특성을 `블록 레벨 스코프`라고 한다.
자바스크립트는 함수 레벨 스코프를 지원했지만 ES6부터 블록 레벨 스코프를 지원하기 시작했다.

ES6의 `let`, `const` 키워드는 블록 레벨 스코프를 가진다.

```js
function foo() {
  if (true) {
    let x = "블록 레벨 스코프";
    console.log(x); // 블록 레벨 스코프
  }
  console.log(x); // ReferenceError
}
```

<br>

## 렉시컬 스코프

스코프가 결정되는 시점을 기준으로 동적 스코프와 정적 스코프로 나뉜다.

- 동적 스코프: 프로그램 런타임 도중 실행 컨텍스트나 호출 컨텍스트에 의해 스코프가 결정
- 정적 스코프(렉시컬 스코프): 함수가 정의된 시점에 스코프가 결정

자바스크립트는 렉시컬 스코프를 따르기 때문에 함수가 선언되자마자 스코프가 결정되고 이후 해당 함수에 의해 함수 객체가 생성되면 해당 함수 객체는 본인의 상위 스코프의 참조를 저장하게 되어 자신의 상위 스코프를 항상 알 수 있게 된다.

<br>

### 렉시컬 환경

함수가 호출이 되면 자바스크립트 엔진에 의해 다음과 같은 흐름이 진행된다.

> 함수의 실행컨텍스트 생성 -> 실행 컨텍스트 스택에 push -> 렉시컬 환경 생성

렉시컬 환경이란 어떠한 코드가 어디서 실행되고 주변에 어떤 코드가 있는지 대체적인 정보를 담고 있는 환경이라고 할 수 있다.
렉시컬 환경은 함수 본인 내부의 식별자, 식별자에 바인딩된 값, 상위 렉시컬 환경을 기억하고 있는 하나의 자료구조이다

<br>

# 클로저(Closure)란?

클로저라는 개념은 자바스크립트를 공부하면서 정말 모호했던 개념이다. 하지만 스코프를 학습하면서 좀 더 정확하게 파악할 수 있었다.

먼저 클로저에 대해 간단하게 정의하면 외부함수에 접근하는 내부함수로 외부함수가 소멸되더라도 외부함수의 환경을 기억하고 있다는 개념이다.
여기서 말한 환경이란 렉시컬 스코프를 뜻하고 함수는 선언되자마자 스코프가 결정이 되고 상위 스코프의 참조를 저장한다고 했다.

코드를 한번 살펴보겠다.

```js
var x = 1;
function foo() {
  var x = 10;
  function bar() {
    console.log(x);
  }

  return bar;
}
var baz = foo();
baz(); // 10
```

1. `foo`를 호출하여 `baz`에 `bar`를 불러오고 `foo`는 종료된다.
2. `baz`를 실행하여 `bar`를 호출한다.
3. `x`를 참조하기 위해 상위 스코프로 올라가며 `x`를 찾는다.
4. `bar`는 상위 스코프의 참조를 가지고 있다. 따라서 `foo`는 소멸되었지만 렉시컬 환경을 기억하고 있다.
5. `foo`에서 선언된 `x` 10을 출력한다.

위 과정을 보면 내부함수인 `bar`가 외부함수 `foo`의 환경을 기억하고 변수 `x`를 참조했다.

원래 `foo`함수의 종료와 함께 렉시컬 환경까지 소멸되어야 하지만 내부 함수인 `bar`가 렉시컬 환경을 참조하기 때문에 가비지 컬렉션의 대상이 되지않는다.
따라서 `baz`에 의해 `bar`가 호출되었을 때 `foo`함수의 `x`를 참조할 수 있게 된다. 이것이 클로저라는 개념이다.

## 클로저의 활용

클로저는 자신이 생성될 때의 상위 스코프의 환경을 기억해야 하므로 메모리 차원에서 손해를 볼 수 있다.
하지만 클로저는 자바스크립트의 강력한 기능으로 이를 적극적으로 사용해야 한다. 클로저가 유용하게 사용되는 상황에 대해 살펴보자.

### 1. 상태 유지

클로저가 가장 유용하게 사용되는 상황은 `현재 상태를 기억하고 변경된 최신 상태를 유지`하는 것이다.

```js
<!DOCTYPE html>
<html>
  <body>
    <button class="toggle">toggle</button>
    <div
      class="box"
      style="width: 100px; height: 100px; background: red;"
    ></div>

    <script>
      var box = document.querySelector(".box");
      var toggleBtn = document.querySelector(".toggle");

      var toggle = (function () {
        var isShow = false;

        // ① 클로저를 반환
        return function () {
          box.style.display = isShow ? "block" : "none";
          // ③ 상태 변경
          isShow = !isShow;
        };
      })();

      // ② 이벤트 프로퍼티에 클로저를 할당
      toggleBtn.onclick = toggle;
    </script>
  </body>
</html>
```

isShow는 box요소의 상태를 나타내고 클로저를 이벤트로 할당했다. 이벤트가 사라지지 않는 한 isShow는 사라지지 않고 이벤트가 발생할 때 마다 변경되며 최신 상태를 유지한다.

<br>

### 2. 정보의 은닉

```js
function Counter() {
  // 카운트를 유지하기 위한 자유 변수
  var counter = 0;

  // 클로저
  this.increase = function () {
    return ++counter;
  };

  // 클로저
  this.decrease = function () {
    return --counter;
  };
}

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.decrease());
```

생성자 함수 Counter는 increase와 decrease 메소드를 갖는다. 하지만 counter는 프로퍼티가 아닌 변수이다.
따라서 counter는 생성자 함수 외부에서 접근이 안된다. 하지만 내부에 선언된 메소드는 클로저이기 때문에 Counter의 렉시컬 환경의 counter변수에 접근할 수 있다.
이러한 특징을 사용해 클래스 기반 언어의 `private` 키워드를 흉내낼 수 있다.

<br>

# Reference

- [클로저(closure)의 개념](https://poiemaweb.com/js-closure)
- [[10분 테코톡] 🍧 엘라의 Scope & Closure](https://www.youtube.com/watch?v=PVYjfrgZhtU)
- [자바스크립트의 스코프와 클로저](https://meetup.toast.com/posts/86)