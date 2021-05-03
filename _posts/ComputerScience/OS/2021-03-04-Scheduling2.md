---
layout: post
title: 운영체제 - 스케쥴러의 이해
tags: [OS]
permalink: /docs/OS/Scheduling2
date: 2021-03-04 04:00:00
---

# 스케쥴러의 이해

## 스케쥴링 알고리즘 기본

### 프로세스(Process)

프로그램은 보조 기억장치(HDD, SSD)에 존재하는 명령어와 정적인 데이터의 조합

- 프로세스는 메모리에 적재되어 실행 중인 프로그램

- 프로그램은 여러 개의 프로세스가 상호작용을 하면서 실행될 수도 있음
- 여러 프로그램을 만들어서 서로 통신하는 프로그램을 작성할 수도 있음 (IPC)

### 스케쥴링 알고리즘

프로세스의 실행 순서를 정하는 알고리즘으로 시분할 시스템, 멀티 프로그래밍의 달성이 목표

- 시분할 시스템: 프로세스 응답 시간을 가능한 한 짧게
- 멀티 프로그래밍: CPU 활용도를 최대로 높임 
  (한 프로세스가 I/O 입출력으로 대기하는 동안 다른 프로세스 처리)

1. FIFO (First In First Out)
   - 가장 간단한 스케쥴러로 배치 처리 시스템과 유사
   - FCFS (First Come First Served)
2. SJF(Shortest Job First)
   - 실행 시간이 가장 짧은 프로세스부터 먼저 실행시키는 알고리즘
   - 프로세스의 실행 시간을 알아야 함
3. Priority-based
   - 정적 우선순위: 프로세스마다 우선순위를 미리 지정
   - 동적 우선순위: 스케쥴러가 상황에 따라 우선순위를 동적으로 변경
4. Round Robin
   - 모든 프로세스가 같은 우선순위를 가지고 time slice를 기반으로 스케쥴링
   - Time slice burst가 일어나면 해당 프로세스는 scheduling queue의 끝으로 이동

## 프로세스 기반 스케쥴링 알고리즘

### 프로세스 상태

![프로세스 상태](https://eunhyejung.github.io/assets/contents/content07.PNG)

- running: 현재 CPU에서 실행 상태
- ready: CPU에서 실행 가능 상태
- block: 특정 이벤트 발생 대기 상태

## 선점형과 비선점형 스케쥴러

### 스케쥴러 구분

정책, policy라고도 함

- 선점형 스케쥴러 (Preemptive Scheduling)
  - 하나의 프로세스가 다른 프로세스 대신 CPU를 차지할 수 있음
  - 프로세스 running 중에 스케쥴러가 이를 중단시키고 다른 프로세스로 교체 가능
  - Round Robin
- 비선점형 스케쥴러 (Non-preemptive Scheduling)
  - 하나의 프로세스가 끝나지 않으면 다른 프로세스는 CPU를 사용할 수 있음
  - 프로세스가 자발적으로 waiting 상태로 들어가거나 실행이 끝났을 때만 다른 프로세스로 교체 가능
  - FIFO(FCFS), SJF, Priority-based

### 스케쥴링 알고리즘 조합

- O(1) 스케쥴러: 두 개의 우선순위 배열로 이루어진 실행 큐를 가지는 스케쥴러. 실행 큐의 각 배열은 타임 퀀텀이 남아있는 active 프로세스와 타임 퀀텀을 모두 사용한 expired 프로세스를 관리한다. 스케쥴러는 실행 큐의 active 배열에 있는 가장 우선순위가 높은 프로세서를 선택하여 실행한다. active 배열이 비면 두 배열의 포인터를 맞바꿔 expired 배열이 active 배열로 바뀐다.

- CFS(Complete Fair Scheduler): 프로세스를 vruntime에 따라 RB-Tree 구조를 가진 run queue에 배치

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)