---
layout: single
title: "[Javascript] 불변 객체"
---

# 불변 객체

불변 객체는 React, Vue, Angular 등의 라이브러리나 프레임워크뿐만 아니라 함수형 프로그래밍, 디자인 패턴 등에서 기초가 되는 중요한 개념이다.

자바스크립트의 참조형 데이터인 Object는 값을 복사할 때 주소값을 복사한다. 그렇기 때문에 참조형 데이터는 내부의 프로퍼티를 변경할 때 기존 데이터가 변경된다.

하지만 객체의 내부 프로퍼티에 변경을 가하더라도 원본 객체는 변하지 않아야 하는 경우가 종종 있는데 이럴 때 불변 객체가 필요하다.

```js
var user = {
  name: "Kim",
  gender: "male",
};

var changeName = function (user, newName) {
  var newUser = user;
  newUser.name = newName;
  return newUser;
};

var user2 = changeName(user, "Park");
if (user !== user2) {
  console.log("유저 정보 변경");
}

console.log(user.name, user2.name); // Park Park;
console.log(user === user2); // true
```

위 코드는 객체의 가변성으로 인한 문제를 보여준다.

user 객체의 name 프로퍼티를 `Park`으로 바꾸는 함수 `changeName`을 호출해서 결과를 user2에 담았다. 따라서 유저 정보가 변경되었다는 콘솔이 출력되어야 하지만 user와 user2는 동일하기 때문에 그냥 통과가 된다.

위 코드처럼 객체의 정보가 바뀌기 전 후의 차이를 보여줘야 하는 기능을 구현하려면 불변성을 확보할 필요가 있다.

# 불변 객체를 만드는 방법

객체의 불변성을 유지하기 위해 객체를 복사하여 수정된 값을 반환하도록 하는 방법을 사용한다.

자바스크립트에서 객체를 복사하는 방법은 여러가지가 있지만 크게 `얕은 복사`와 `깊은 복사`로 나눌 수 있다.

## 얕은 복사

얕은 복사는 바로 아래 단계의 값만 복사하는 방법이다.

1. `Object.assign(target, source)`

Object.assign은 ES6에 추가된 메소드이며 target 객체로 source 객체의 프로퍼티를 복사한다. 리턴값으로 target 객체를 반환한다.

```js
var user = {
  name: "Kim",
  gender: "male",
};

var user2 = Object.assign({}, user);
user2.name = "Park";

onsole.log(user.name, user2.name); // Kim Park;
console.log(user === user2); // false
```

2. `Spread Operator`

```js
var user = {
  name: "Kim",
  gender: "male",
};

var user2 = { ...user };
user2.name = "Park";

onsole.log(user.name, user2.name); // Kim Park;
console.log(user === user2); // false
```

3. `for 문`

```js
var user = {
  name: "Kim",
  gender: "male",
};

var user2 = {};

for (var prop in user) {
  user[prop] = user[prop];
}
onsole.log(user.name, user2.name); // Kim Park
console.log(user === user2); // false
```

얕은 복사의 문제점은 중첩 객체에서 내부 프로퍼티의 객체는 주소값을 복사하기 때문에 사본을 바꾸면 원본도 바뀌는 문제가 있다.

```js
var user = {
  name: "Kim",
  urls: {
    github: "github.com",
    facebook: "facebook.com",
  },
};

user2 = Object.assign({}, user);
user2.urls.github = "";

console.log(user.urls.github, user2.urls.github); // '' ''
console.log(user.urls === user2.urls); // true
```

이런 현상이 발생하지 않게 하려면 user.urls 프로퍼티에 대해서도 불변성을 유지할 필요가 있다. 이를 위해 중첩된 객체에 대해 깊은 복사를 수행한다.

## 깊은 복사

깊은 복사는 내부의 모든 값드를 하나하나 찾아서 전부 복사하는 방법이다.

1. `재귀 호출`

```js
var copyObjDepp = function (target) {
  var result = {};
  if (typeof target === "object" && target !== null) {
    for (var prop in target) {
      result[prop] = copyObjDeep(target[prop]);
    }
  } else {
    result = target;
  }

  return result;
};
```

target이 객체인 경우 내부 프로퍼티를 순회하며 재귀적으로 호출하여 중첩된 객체까지 완전히 복사해주는 방법이다.

2. `JSON 객체 메소드`

```js
var user = {
  name: "Kim",
  urls: {
    github: "github.com",
    facebook: "facebook.com",
  },
};

var user2 = JSON.parse(JSON.stringify(user));
```

객체를 JSON 문법으로 표현된 문자열로 전환한 후 다시 JSON 객체로 바꾸는 방법이다.

매우 단순하면서도 잘 동작하지만, 메서드(함수)나 숨겨진 프로퍼티인 **proto**, getter/setter 등은 JSON.stringify 메소드가 처리할 수 없다는 문제가 있다.

httpRequset로 받은 데이터를 저장한 객체를 복사할 때 등 순수한 정보만 다룰 때 활용하기 좋은 방법이다.

# 정리

불변 객체는 최근 자바스크립트에서 중요하게 다루어지는 개념 중 하나이다. 참조형 데이터인 객체를 불변값으로 사용해야 하는 경우가 있으며 이를 위해 내부 프로퍼티들을 일일이 복사해 주어야 한다.

앞서 다루지 않았지만 immutable.js, lodash 등의 라이브러리를 사용하는 방법도 있다.
