---
layout: post
title: 운영체제 - 스케쥴링 - 배치 처리, 멀티 태스킹, 멀티 프로세싱, 멀티 프로그래밍
tags: [OS]
permalink: /docs/OS/Scheduling1
date: 2021-03-02 10:00:00
---

# 스케쥴링 - 배치 처리, 멀티 태스킹, 멀티 프로세싱, 멀티 프로그래밍

## 배치 처리 시스템(batch processing)

- 실행요청 순서에 따라 순차적으로 프로그램을 실행하는 시스템으로 queue와 같은 방식으로 동작함

![Batch Processing](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/batch.png)

- 배치 처리 시스템의 문제점
  - 어떤 프로그램은 실행 시간이 너무 길어서 다른 프로그램이 오래 기다려야 함
    위의 Case 1에서 Application 1을 실행하기 위해서는 5시간을 기다려야 함
  - 동시에 여러 작업을 수행할 수 없음
  - 다중 사용자를 지원할 수 없음

## 시분할 시스템(Time Sharing System, TSS)

- 다중 사용자 지원을 위해 컴퓨터 응답 시간을 최소화하는 시스템

![Time Sharing System](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/time-sharing.png)

## 멀티 태스킹(Multi-tasking)

- 단일 CPU에서 여러 응용 프로그램을 동시에 실행할 수 있는 시스템으로 실제로 동시에 프로그램을 실행하는 것이 아니라 시분할 시스템처럼 여러 개의 응용 프로그램을 매우 짧은 시간 간격으로 전환하며 실행

![Multi-tasking](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/multi-tasking.png)

## 멀티 프로세싱(Multi-processing)

- **여러 CPU에 하나의 프로그램**을 병렬로 실행해서 실행 속도를 극대화하는 시스템

![Multi-processing](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/multi-processing.png)

## 멀티 프로그래밍(Multi-programming)

- CPU를 최대한 많이 활용하기 위하여 한 응용 프로그램이 I/O 입출력 등으로 인하여 CPU를 사용하지 않는 동안 다른 프로그램을 실행하도록 함

![Multi-programming](https://s3.ap-northeast-2.amazonaws.com/static.gracieuxyh.dev/os/multi-programming.png)

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)