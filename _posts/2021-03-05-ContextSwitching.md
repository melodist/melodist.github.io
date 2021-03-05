---
layout: post
title: 운영체제 - 컨텍스트 스위칭
tags: [OS]
permalink: /docs/OS/ContextSwitching
date: 2021-03-05 10:25:00
---
# 컨텍스트 스위칭

### 프로세스와 컨텍스트 스위칭

![ContextSwitching](https://user-images.githubusercontent.com/52024566/110089382-691fd080-7dd9-11eb-8c3e-04b1e5e41d14.png)

- Context Switching (문맥 교환): CPU에서 실행할 프로세스를 교체하는 기술

1. 실행 중지할 프로세스 정보를 해당 프로세스의 PCB에 업데이트해서 메인 메모리에 저장
2. 다음 실행할 프로세스 정보를 메인 메모리에 있는 해당 PCB 정보(PC, SP)를 CPU의 레지스터에 넣고 실행

### Process Control Block (PCB)

![PCB](https://user-images.githubusercontent.com/52024566/110055961-4a054c80-7da1-11eb-9749-a4b8c6c41729.png)





## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)