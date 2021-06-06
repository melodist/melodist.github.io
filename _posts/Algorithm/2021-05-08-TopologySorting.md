---
layout: post
title: 위상 정렬
tags: [Algorithm]
permalink: docs/Algorithm/Sortings
use_math: true
date: 2021-05-08 11:17:00
---
# 위상 정렬 (Topological Sort)
## 위상 정렬
하나의 DAG (Directed Acyclic Graph, 사이클이 없는 방향 그래프)인 $G=(V, E)$의 위상 정렬은 $G$가 간선 $(u, v)$를 가질 때, $u$가 $v$보다 순서상으로 먼저 나타나도록 모든 정점을 선형으로 나열하는 것이다. 이는 다음과 같은 간단한 알고리즘으로 나타낼 수 있다.

1. 각 정점 $v$에 대해 종료 시간  $v.f$를 계산하기 위해 DFS($G$)를 호출한다.
2. 각 정점이 종료될 때마다 연결 리스트의 맨 앞에 삽입한다.
3. 정점의 연결 리스트를 반환한다.

## 위상 정렬의 구현
1. 진입 차수가 0인 정점을 선택
   - 진입 차수가 0인 모든 정점을 큐에 삽입
2. 선택된 정점과 연결된 모든 간선을 제거
   - 선택된 정점을 큐에서 삭제하고 연결된 정점에 대해 진입 차수를 감소
   - 진입 차수가 0이 된 정점은 새로 큐에 삽입
3. 모든 정점이 선택될 때까지 위의 과정을 반복

## 위상 정렬 예시
[백준 1005 - ACM Craft](https://www.acmicpc.net/problem/1005)
[백준 2252 - 줄 세우기](https://www.acmicpc.net/problem/2252)
