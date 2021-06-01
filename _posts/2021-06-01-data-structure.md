---
layout: single
title: "[자료구조] 스택(Stack), 큐(Queue) 개념 / 비교"
---

# 스택(Stack)이란?

스택(Stack)은 `쌓다` 라는 뜻으로 데이터를 순차적으로 쌓는 자료구조이다.

<center>
<img src="https://user-images.githubusercontent.com/67500926/120292078-f0101680-c2fe-11eb-9a53-3c94bb1f9706.png">
</center>

<br>

## 특징

자료구조 Stack의 특징은 데이터 삽입, 삭제가 하나의 방향으로 이루어지는 제한적 접근에 있다.
이런 Stack 자료구조의 정책을 `LIFO(Last In First Out) 또는 FILO(First In Last Out)라고 한다.
Stack에서는 주로 다음 네 가지 동작이 수행된다.

- **push**: Stack에 데이터 삽입. Stack의 크기를 벗어난 상태를 Overflow라고 한다.
- **pop**: Stack에서 데이터 삭제. 삽입된 역순으로 삭제된다. Stack의 크키가 0 보다 작아지는 상태를 Underflow라고 한다.
- **isEmpty**: Stack의 상태를 반환. 비어있으면 true 그렇지 않으면 false를 반환한다.
- **Peek(Top)**: Stack의 최상단 데이터를 가져온다.

<br>

## 활용예시

- 웹 브라우저의 이전 / 다음 페이지로 이동
- 편집기의 실행취소 기능

<br>

# 큐(Queue)란?

큐(Queue)는 `대기행렬` 이라는 뜻으로 실생활에서 놀이동산에서 줄을 서서 기다리는 것과 형태의 자료구조이다.  
Stack과 반대되는 개념으로, 입력과 출력이 각각 다른 방향에서 이루어진다.

<center>
<img src="https://user-images.githubusercontent.com/67500926/120291577-6a8c6680-c2fe-11eb-9e91-b82a56f695df.png">
</center>
<br>

## 특징

자료구조 Queue의 특징은 먼저 들어간 데이터가 먼저 나오는 FIFO(First In First Out) 또는 LILO(Last In Last Out) 방식이다.
한쪽으로만 삽입, 삭제가 이루어지는 Stack과 달리 Queue는 한쪽 끝에서 삽입, 반대쪽 끝에서 삭제가 이루어진다.
Queue에서는 주로 다음 네 가지 동작이 수행된다.

- **Enqueue**: Queue에 데이터 삽입.
- **Dequeue**: Queue에서 데이터 삭제. 삽입된 순서대로 삭제된다.
- **Front**: Queue에서 가장 앞에 있는 데이터를 가져온다.
- **Rear**: Queue에서 마지막에 있는 데이터를 가져온다.

## 예시

- CPU, Disk 스케쥴링
- 두 프로세스간 비동기 데이터 전송(IO Beffers, file IO)

<br>

# 비교

Stack과 Queue의 가장 큰 차이점은 데이터 삭제에 있다.
Stack에서는 가장 최근에 추가 된 데이터를 삭제하고 Queue에서는 가장 먼저 추가 된 데이터를 삭제한다.

<br>

## 자바스크립트의 Stack과 Queue

자바스크립트에서 `push()`, `pop()`, `shift()` 같은 배열 내장함수를 통해 Stack과 Queue 동작을 쉽게 수행할 수 있다.
하지만 배열을 통해 Queue를 사용했을 때 주의할 점이 있다.

Stack은 한방향으로 데이터 삽입(push)과 삭제(pop)가 이루어지기 때문에 배열의 끝에서 삽입, 삭제를 처리할 수 있다. 따라서 시간복잡도가 O(1)이 된다.
하지만 Queue는 배열의 앞부분에서 데이터를 삭제(shift)하기 때문에 나머지 모든 데이터들의 인덱스 조정이 필요하다. 따라서 시간복잡도는 O(N)이 된다.
배열의 길이가 짧다면 문제가 안되겠지만 수천만 이상일 경우 배열이 아닌 다른 자료구조(`Linked List`)를 고려해야한다.

따라서 데이터의 효율적인 처리를 위해 적절한 자료구조를 선택하는 것이 중요하다.
