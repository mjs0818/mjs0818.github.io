---
layout: single
title: "함수형 프로그래밍 개념"
---

절차지향 프로그래밍, 객체지향 프로그래밍, 함수형 프로그래밍 이런 것들은 프로그래밍 패러다임이라고 한다. 프로그램을 만들 때 원하는 목표에 따라 적절한 방법과 기법을 활용해 프로그램을 만들어 나가게 된다. 

많은 프로그래밍 언어 중에 함수형 프로그래밍에 특화된 언어가 있고, 자바스크립트, 자바, 코틀린, 고  객체지향 프로그래밍을 지원하면서 함수형 프로그래밍에서 사용하는 몇가지 특징을 접목한 언어도 있다.

함수형 프로그래밍이라는 개념은 아주 오래전부터 등장했다. 함수형 프로그래밍은 수학의 원리와 밀접하게 연관이 있기 때문에 배우는데 시간이 오래걸리고 사람이 이해하는데 난해할 수도 있다.
따라서 사람의 사고방식과 가장 가까운 절차지향 프로그래밍, 객체지향 프로그래밍이 보편적으로 많이 쓰여왔다. 
그런데 왜 최근에 함수형 프로그래밍에 대한 관심을 가지게 된걸까?

AI, IoT, 빅데이터, 비트코인이 뜨기 시작하면서 방대한 데이터를 안정적이고 빠르게 처리하는 것의 중요성이 부각되면서 함수형 프로그래밍언어가 주목받기 시작했다.

# 함수형 프로그래밍이란?
함수형 프로그래밍을 파이프라인과 비유해볼 수 있는데 인풋을 넣으면 어떤 처리과정을 거쳐 아웃풋이 나온다. 외부에서는 파이프라인 내부상태를 보거나 접근할 수 없고 파이프라인 내부에서도 외부로 접근을 할 수가 없다
함수형 프로그래밍으로 프로그래밍을 만든다는 것은 이런 함수들을 적용하고 묶어서 프로그램을 구성해나가는 것을 말한다.

오래전부터 꾸준히 많이 사용되어진 객체지향 프로그래밍을 단순히 함수형 프로그래밍으로 대체할 수는 없다. 
하지만 많은 프로그래밍 언어의 최신버전에 함수형 프로그래밍의 특징들이 더해지고 있고 함수형 프로그래밍들의 특징을 잘 활용하면 더 안정성있는 프로그래밍을 해나갈 수 있다.

# 함수형 프로그래밍의 특징
## Pure Functions (순수함수)
함수에서 외부의 상태 값을 참조하거나 변경하면 순수함수가 아니다.
```js
let num = 1;

function add(a) {
  return a + num;
```

동일한 인자를 넣었을 때 항상 동일한 결과값을 반환하고 언제 선언되었는지 외부의 영향을 전혀 받지 않도록 함수를 작성해야 한다.

```js
function add(a, b) {
  return a + b;
}

const result = add(2, 3);
```

## Stateless, Immutablility (비상태, 불변성)
비상태, 불변성을 유지해야 한다.
함수에 인자로 전달된 데이터를 변경하는 것은 함수형이 아니다.
```js
let person = { name: '김코딩', age: 20 };

function increaseAge(person) {
  person.age = person.age + 1;
  return person
}
```

함수형 프로그래밍에서는 함수로 전달된 데이터를 변경하는 것이 아니라 새로운 오브젝트를 만들어서 결과를 전달해야 한다. 

외부의 상태나 함수에 인자로 전달된 데이터의 상태를 변경하지 않음으로써 side effect(부작용)를 만들지 않으므로 불변성을 유지하기 때문에 여러가지 동시다발적인 멀티쓰레딩 환경에서도 안정적으로 동작할 수 있다. 

> side effect: 함수를 호출하면 외부의 상태가 변경되거나 예상하지 못한 에러가 발생하는 것

자바스크립트 언어에서는 불변성 데이터 타입이라는 것이 따로 없기 때문에 Object.freeze를 사용해 오브젝트를 불변성으로 만들어 줄 수 있다.

```js
const person = Object.freeze({ name: '김코딩', age: 20 });

function increaseAge(person) {
  return Object.freeze({...person, age: person.age + 1});
}
```

## Expression Only (표현식)
Expression이란 간단하게 말하면 값으로 평가되는 것을 말한다. 
```js
// 표현식

// 리터럴 100은 값으로 평가
var number = 100;

// 50 + 50은 100으로 평가
var sum = 50 + 50

// 변수 식별자를 참조하면 변수 값으로 평가
sum;

// 함수 또는 메소드 호출도 값으로 평가

add(1, 2); // 1 + 2 결과 값
person.getName(); // person 객체의 name 
```

if, for, switch 같은 문(Statement)을 사용하는 것은 함수형 프로그래밍이 아니다.
```js
let number = [1, 2, 3];

function multiply(numbers, multiplier) {
  for (let i = 0; i < numbers.length; i++) {
    number[i] = numbers[i] * multiplier;
  }
}
```

함수형 프로그래밍에서는 if, for, switch 같은 문(Statement)을 사용하지 않고 프로그래밍을 해나가야 한다.
```js
function multipley(numbers, multiplier) {
  return numbers.map(num => num * multiplier);
}
```
## First-class and higher-order functions (일급함수, 고차함수)
일급함수란 함수가 다른 데이터처럼 변수에 할당되거나 함수에 인자로 전달되거나 리턴되는 것을 말한다.

```js
// first-class functnions
const addTwo = a => a + 2;
const multiplyTwo = a => a * 2;
const transform = numbers => numbers.map(addTwo).map(multiplyTwo);
console.log(transform([1, 2, 3, 4]); // [6, 8, 10, 12]
```

 
고차함수란 함수 자체를 인자로 전달받거나 함수에서 또다른 함수를 리턴받는 함수를 말한다.

```js
// higher-order functions
const addToppings = topping => food => food + topping;
const egg = addToppings('🍳');
const bacon = addToppings('🥓');

console.log(egg('🍲'));    // 🍲🍳 
console.log(bacon('🥪'));  // 🥪🥓
```

함수형 프로그래밍을 하기 위해서 일급함수와 고차함수의 속성을 가지고 있어야 한다.

