## *PriorityQueue* (우선순위 큐)

*Queue*는 일반적으로 **FIFO**(First In First Out) 의 형식을 가진다.

*PriorityQueue*는 *Queue* 인터페이스 중 하나로, <u>저장 순서 상관 없이 우선순위가 높은 것 부터 선출</u>하는 것이 특징이다.

저장공간으로 배열을 사용하고, 각 요소를 힙이라는 자료구조 형태로 저장한다.
힙은 이진트리의 한 형태로 가장 큰 값과 가장 작은 값을 빠르게 찾을 수 있다는 장점이 있다.

*PriorityQueue* 요소에 대한 비교 기준이 존재해야 한다. 비교 기준에 따라서 그 우선 순위를 결정하게 된다.

### *PriorityQueue* 선언

```java
PriorityQueue pq = new PriorityQueue<>();
pq.add(3);
pq.add(4);
pq.add(1);
pq.add(10);

while (!pq.isEmpty()) {
    System.out.println(pq.poll());
} // 1, 3, 4, 10

// 내림차순 선언
PriorityQueue pq = new PriorityQueue<>(Collections.reverseOrder());
```

### *PriorityQueue* 메서드의 종류

#### 추가

```java
pq.add(1);
pq.offer(10);
```

- `add()`를 사용하여 추가하는 것은 내부적으로 `offer()`로 추가됩니다.
//TODO: add()와 offer()는 어떻게 다를까?
- *PriorityQueue* 를 생성하면 기본 사이즈로 생성되고, 더 맣은 값을 저장한다면 자동으로 늘어난다.

#### 삭제

```java
pq.poll(); // 가장 앞에 있는 값을 return 하고 삭제
pq.remove(); // 가장 앞에 있는 값을 삭제
pq.clear(); // 초기화
```

#### 조회

```java
pq.peek(); // 가장 앞에 있는 값을 확인
```

### *PriorityQueue* 활용 예시

#### 1

중복이 허용된 문자열 배열이 주어진다. 중복이 적은 문자열부터 나열된 배열을 구하라.

```
problem = [a, b, b, c, c, c]
expected = [a, b, c]
```

#### 2

[프로그래머스 디펜스 게임](https://school.programmers.co.kr/learn/courses/30/lessons/142085#)


