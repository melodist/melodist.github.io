---
layout: post
title: 시스템 프로그래밍 - 쉘로 시작하는 시스템 프로그래밍 기본
tags: [OS, Linux, SystemProgramming]
permalink: /docs/OS/SystemProgramming1
date: 2021-03-23 17:55:00
---
# 쉘로 시작하는 시스템 프로그래밍 기본

## 다중 사용자 지원

- whoami: 로그인한 사용자 ID를 알려줌

```bash
# whoami
root
```

- passwd: 로그인한 사용자 ID의 암호 변경

```bash
# passwd
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

- useradd와 adduser
  - useradd는 사용자 기본 설정을 자동으로 하지 않지만 adduser는 사용자 기본 설정을 자동으로 수행

```bash
# adduser dave
Enter UNIX password:
Retype UNIX password:
```

- su: 사용자 변경
  - su root: 현재 사용자의 환경설정을 기반으로 root로 전환
  - su - root: 변경되는 사용자의 환경설정을 기반으로 root로 전환
- sudo: root 권한으로 실행하기
  - root 계정으로 접속하지 않은 상태에서 root 권한이 필요한 명령을 실행
  - /etc/sudoers 설정 파일에서 다음과 같이 설정을 변경할 수 있음

```
1. 특정 사용자가 sudo를 사용할 수 있도록 설정
	userid	ALL=(ALL)	ALL
2. 특정 그룹에 포함된 모든 사용자가 sudo를 사용할 수 있도록 설정
	%group	ALL=(ALL)	ALL
3. 패스워드 생략 설정
	%group	ALL=(ALL)	NOPASSWD: ALL
    userid	ALL=(ALL)	NOPASSWD: ALL
```

## 파일 및 권한 관리

## 리다이렉션과 파이프

## foreground와 background 프로세스

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)