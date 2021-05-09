---
layout: post
title: Longest Subsequence
tags: [Algorithm]
permalink: docs/Algorithm/LongestSubsequence
use_math: true
date: 2021-05-09 11:07:00
---
# Longest Subsequence
## Longest Increasing Subsequence (LIS)
LIS를 길게 하기 위해서는 LIS 조건을 만족하면서 LIS 내의 원소 값이 가장 작도록 LIS를 유지해야 한다. 따라서, 배열을 처음부터 확인하면서 LIS를 유지하기 위한 최적의 위치에 원소를 삽입한다.

1. LIS의 맨 뒤 원소보다 현재 원소의 값이 클 경우 LIS 맨 뒤에 현재 원소를 삽입한다.
2. 현재 원소의 값보다 작을 경우 이분탐색을 이용하여 현재 원소가 삽입될 위치를 찾는다.

**배열 A의 LIS를 구하는 예시**

```python
import bisect


LIS = [A[0]]

for a in A[1:]:
    if a > LIS[-1]:
        LIS.append(a)
    else:
        lower_bound = bisect.bisect_left(LIS, a)
        LIS[lower_bound] = a
```
## Longest Common Substring

Subsequence는 연속적인 부분 문자열을 일컫는다. Longest Common Substring을 구하기 위하여 Dynamic Programming을 이용한다. 두 문자열 S1, S2에 대하여 LCS를 구하는 방법은 다음과 같다.

1. $S1[n] == S2[k]$일 경우 $DP[n][k] = DP[n-1][k-1] + 1$
2. $S1[n] != S2[k]$일 경우 $DP[n][k] = 0$

```python
prev = [''] * (len(s2)+1)
curr = [''] * (len(s2)+1)

for r in s1:
    for j, c in enumerate(s2, 1):
        if r == c:
            curr[j] = prev[j-1] + c
            
    prev, curr = curr, prev
```
## Longest Common Subsequence

Subsequence는 연속적이지 않은 부분 문자열을 일컫는다. Longest Common Subsequence를 구하기 위하여 Dynamic Programming을 이용한다. 두 문자열 S1, S2에 대하여 LCS를 구하는 방법은 다음과 같다.

1. $S1[n] == S2[k]$일 경우 $DP[n][k] = DP[n-1][k-1] + 1$
2. $S1[n] != S2[k]$일 경우 $DP[n][k] = max(DP[n][k-1], DP[n-1][k])$

**S1, S2의 Longest Common Subsequence를 구하는 예시**

```python
prev = [''] * (len(s2)+1)
curr = [''] * (len(s2)+1)

for r in s1:
    for j, c in enumerate(s2, 1):
        if r == c:
            curr[j] = prev[j-1] + c
        else:
            curr[j] = prev[j] if len(prev[j]) > len(curr[j-1]) else curr[j-1]
            
    prev, curr = curr, prev
```