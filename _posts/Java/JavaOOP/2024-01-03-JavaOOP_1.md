---
layout: post
title: Java Algorithm 문제 해결에 필요한 라이브러리
tags: [Java]
permalink: docs/Java/Algorithm
date: 2021-07-03 09:30:00
---

## 2차원 배열

```java
String[][] twoDArr1 = new String[5][4];

String[][] twoDArr2 = new String[5][]; // 바깥 배열 크기 결정
for (int i=0; i<twoDArr2.length; i++) {
	twoDArr2[i] = new String[4]; // 가변적으로 안쪽 배열 크기 결정
    for (int j=0; j<twoDArr2[i].length; j++) {
        twoDArr2[i][j] = "A";
    }
}

for (String[] innerArray : twoDArr2) { // for-each문 사용
    for (String s : innerArray) {
        System.out.print(s);
    }
    System.out.println();
}
```

## 자료구조

### Deque (Double-Ended Queue)

**선언**

```java
import java.util.Deque;
import java.util.ArrayDeque;

Deque<Integer> deque = new ArrayDeque<Integer>(); // ConcurrentLinkedDeque, LinkedBlockingDeque, LinkedList
```

**메소드**

`deque.addFirst()`, `deque.offerFirst()`, `deque.push()` : Deque의 First에 원소를 삽입

`deque.add()`,`deque.addLast()`, `deque.offer()`, `deque.offerLast()` : Deque의 Last에 원소를 삽입

`deque.remove()`, `deque.removeFirst()`, `deque.poll()`, `deque.pollFirst()`, `deque.pop()` : Deque의 First 원소를 제거

`deque.removeLast()`, `deque.pollLast()` : Deque의 Last 원소를 제거

`deque.getFirst()`,`deque.peek()`, `deque.peekFirst()` : Deque의 First 값을 반환 (제거하지는 않음)

`deque.getLast()`, `deque.peekLast()` : Deque의 Last 값을 반환 (제거하지는 않음)

`deque.size()` : Deque의 크기 반환

`deque.isEmpty()` : Deque가 비어있는지 확인

`deque.contains(a)` : Deque에 a가 있는지 확인

### Stack

**선언**

```java
import java.util.Stack;

Stack<Integer> stack = new Stack<>();
```

**메소드**

`stack.push()` : 스택의 최상단에 값을 추가

`stack.pop()` : 스택의 최상단에서 값을 하나 제거

`stack.clear()` : 스택의 값을 전부 제거

`stack.peek()` : 스택의 최상단 값을 반환 (제거하지는 않음)

`stack.size()` : 스택의 크기 반환

`stack.empty()` : 스택이 비어있는지 확인

`stack.contains(a)` : 스택에 a가 있는지 확인

### Queue

**선언**

```java
import java.util.Queue;
import java.util.LinkedList;

Queue<Integer> queue = new LinkedList<>();
```

**메소드**

`queue.add()`, `queue.offer()` : Queue의 끝에 값을 추가

`queue.poll()` : Queue의 첫번째 값을 반환하고 제거, Queue가 비어있을 경우 null 반환

`queue.remove()` : Queue의 첫번째 값을 반환하고 제거, Queue가 비어있을 경우 NoSuchElementException

`queue.clear()` : Queue의 값을 전부 제거

`queue.peek()` : Queue의 첫번째 값을 반환 (제거하지는 않음)

`queue.size()` : Queue의 크기 반환

`queue.isEmpty()` : Queue가 비어있는지 확인

`queue.contains(a)` : Queue에 a가 있는지 확인

### Map

**선언**

```java
import java.util.Map;
import java.util.HashMap;

Map<String, Integer> map = new HashMap<String, Integer>(); // Map<Key, Value>, TreeMap
```

**메소드**

`map.put(key, value)` : map에 (key, value) 쌍을 저장

`map.get(key)` : map에서 전달된 key에 대응하는 value를 반환. 값이 없을 경우 null 반환

`map.getOrDefault(key, default)`: map에서 전달된 key에 대응하는 value를 반환. 값이 없을 경우 default 반환

`map.replace(key, value)` : map에서 전달된 key에 대응하는 값을 value로 대체

`map.containsKey(key)` : map에서 key를 포함하고 있는지 확인

`map.containsValue(value)` : map에서 value를 포함하고 있는지 확인

`map.keySet()` : map에 포함되어 있는 모든 key로 만들어진 Set 객체를 반환

`map.remove(key)` : map에서 전달된 key에 대응하는 값을 제거

`map.isEmpty()` : map이 비어있는지 확인

`map.size()`: map의 (key, value) 쌍 개수를 반환

### Set

**선언**

```
import java.util.Set;
import java.util.HashSet;
```

**메소드**

`set.add(a)` : set에 a를 추가함

`set.clear()` : set의 모든 요소를 제거함

`set.contains(a)` : set에 a가 있는지 확인

`set.equals(o)` : set이 o와 같은지 확인

`set.isEmpty()`: set이 비어있는지 확인

`set.iterator()` : set을 Iterator 형태로 반환

`set.remove(a)` : set에서 a를 제거

`set.size()` : set의 원소 개수를 반환

`set.toArray()` : set의 모든 원소를 Object 타입의 배열로 반환

### PriorityQueue

**선언**

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> pq = new PriorityQueue<>();
```

**메소드**

`pq.add()`, `pq.offer()` : pq에 값을 추가

`pq.poll()` : pq의 첫번째 값을 반환하고 제거, pq가 비어있을 경우 null 반환

`pq.remove()` : pq의 첫번째 값을 반환하고 제거, pq가 비어있을 경우 NoSuchElementException

`pq.clear()` : pq 내의 원소를 전부 제거

`pq.peek()` : pq의 첫번째 값을 반환 (제거하지는 않음)

`pq.size()` : pq의 크기 반환

`pq.isEmpty()` : pq가 비어있는지 확인

`pq.contains(a)` : pq에 a가 있는지 확인

## 입출력

### BufferedReader

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;

public Class main {
    
	public static void main(String args[]) throws IOException { // 예외 처리 필수
        BufferedReader bf = new BufferedReader(new InputStreamReader(System.in));
        String s = bf.readLine(); // String
        int i = Integer.parseInt(bf.readLine()); // int
    }
}
```

### StringTokenizer

**생성자**

`StringTokenizer st = new StringTokenizer(String str)` : 띄어쓰기 기준으로 str 분리

`StringTokenizer st = new StringTokenizer(String str, String delim)` : delim 기준으로 str 분리

`StringTokenizer st = new StringTokenizer(String str, String delim, boolean flag)` : delim을 토큰에 포함시킬지 flag로 결정

**메소드**

`st.countTokens()` : 전체 토큰의 수를 반환

`st.hasMoreTokens()` : 토큰이 남아있는지 확인

`st.nextToken()` : 다음 토큰을 반환

```java
import java.util.StringTokenizer;

public Class main {
    
	public static void main(String args[]) throws IOException { // 예외 처리 필수
        String s1 = "a b c d";
        String s2 = "a,b,c,d";
        
        StringTokenizer st1 = new StringTokenizer(s1);
        StringTokenizer st2 = new StringTokenizer(s1, ",");
        
        while(st.hasMoreTokens()) {
            System.out.println(st.nextToken());
        }
    }
}
```

## 이진 탐색

```java
import java.util.Arrays;

Arrays.binarySearch(arr, target);
```

target이 arr에 있을 경우 index를 반환, 없을 경우 arr[i] < target < arr[i+1]일 때, `-(i+2)` 반환

| index          |        | 0    |        | 1    |        | 2    |        | 3    |        |
| -------------- | ------ | ---- | ------ | ---- | ------ | ---- | ------ | ---- | ------ |
| value          | 1      | 2    | 3      | 4    | 5      | 6    | 7      | 8    | 9      |
| binarySearch() | **-1** | 0    | **-2** | 1    | **-3** | 2    | **-4** | 3    | **-5** |
