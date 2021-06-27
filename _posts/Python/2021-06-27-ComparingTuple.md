---
layout: post
title: Python Tuple 비교 방법
tags: [Python]
permalink: /docs/Python/ComparingTuple
date: 2021-06-27 22:25:00
---
# Python Tuple 비교 방법

## 비교 원칙

1. 두 tuple의 0번째 인덱스부터 비교를 시작하여 두 값이 같을 경우 다음 인덱스의 값을 비교한다
2. 두 값이 같지 않을 경우 그 비교 결과가 tuple의 비교 결과이다.
3. 두 tuple의 모든 값이 같을 경우 두 tuple은 같다.
4. 한 tuple의 모든 값이 비교되었을 경우 그 tuple은 나머지 tuple보다 작다.

## 예제

```python
p1 = (1, 2)
p2 = (1, 3)
print("p1 >= p2 :", p1 >= p2)
# p1 >= p2 : True
```
위의 예제는 아래의 예제와 같은 의미이다.

```python
if p1[0] > p2[0]:
	return True
elif p1[0] == p2[0] and p1[1] >= p2[1]:
    return True
else:
    return False
```

```python
p1 = (1, 2, 3)
p2 = (1, 2, 3, 0)
print("p1 < p2 :", p1 < p2)
print("p1 > p2 :", p1 > p2)
# p1 < p2 : True
# p1 > p2 : False
```

## 참고

[Python – Comparing tuples](https://howtodoinjava.com/python/misc/compare-tuples/)

[백준 17387 - 선분 교차 2](https://www.acmicpc.net/problem/17387)