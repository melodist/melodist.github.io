---
layout: post
title: 시스템 프로그래밍 - 프로세스 관리
tags: [OS, Linux, SystemProgramming]
permalink: /docs/OS/SystemProgramming3
date: 2021-03-25 17:20:00
---
# 프로세스 관리

## 프로세스 ID

### 프로그램, 프로세스, 스레드

- 프로그램: 바이너리, 코드 이미지, 응용 프로그램, Application, 또는 실행 파일
- 프로세스: 실행 중인 프로그램 (메모리 적재 + 프로세스 상태 정보 포함)
- 스레드
  - 리눅스 프로세스는 기본 스레드 포함
  - 싱글스레드 프로세스: 기본 프로세스
  - 멀티스레드 프로세스: 여러 스레드 존재

### 프로세스 ID

- pid, 각 프로세스는 해당 시점에 unique한 pid를 가짐
- pid 최대 값은 $ 2^15 = 32768 $
- `sudo vi /proc/sys/kernel/pid_max`로 가장 최근에 할당된 pid 확인 가능

### 프로세스 계층

- 최초 프로세스: init 프로세스, pid 1
- init 프로세스는 운영체제가 생성
- 다른 프로세스는 또다른 프로세스로부터 생성: 부모 프로세스, 자식 프로세스
- ppid 값이 부모 프로세스의 pid를 뜻함

### 프로세스와 소유자(owner) 관리

- 리눅스 내부에서는 프로세스의 소유자(사용자)와 그룹을 UID/GID (정수)로 관리
- 사용자에 보여줄 때에만 UID와 사용자이름 매핑 정보를 기반으로 사용자 이름으로 제공
- `sudo vi /etc/passwd`로 사용자명, UID, GID 등 확인 가능

### getpid()와 getppid()

- 함수 원형

```c
#include <sys/types.h>
#include <unistd.h>

pid_t getpid (void);
pid_t getppid (void);
```

- 실습 코드

```c
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>

int main()
{
	printf ("pid=%d\n", getpid());
	printf ("ppid=%d\n", getppid());
}
```

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)