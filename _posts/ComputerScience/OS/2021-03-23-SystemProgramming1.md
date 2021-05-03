---
layout: post
title: 시스템 프로그래밍 - 쉘로 시작하는 시스템 프로그래밍 기본
tags: [OS, Linux, SystemProgramming, UF]
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

pwd: 현재 디렉토리 위치

cd: 디렉토리 이동

ls: 파일 목록 출력 (*는 임의 문자열, ?는 문자 하나)

### ls와 파일 권한

![Shell1](https://user-images.githubusercontent.com/52024566/112439658-bebe1c00-8d8c-11eb-9904-aecfafe19e5a.png)

### chmod: 파일 권한 변경

- 기호 문자를 사용하는 방법

![Shell2](https://user-images.githubusercontent.com/52024566/112440223-5f144080-8d8d-11eb-917d-a322618b11b5.png)

- 숫자를 사용하는 방법

![Shell3](https://user-images.githubusercontent.com/52024566/112440227-60456d80-8d8d-11eb-8c77-41a8b25bdbd2.png)

### chown: 소유자 변경

chown [옵션] [소유자:소유그룹] [파일]

cat: 파일 보기

head/tail: 파일 시작/끝부분 보기

more: 화면이 넘어가기 전까지의 파일 보기

### rm: 파일 및 폴더 삭제

- r 옵션 : 하위 디렉토리를 포함한 모든 파일 삭제
- -f 옵션 : 강제로 파일이나 디렉토리 삭제

## 리다이렉션과 파이프

## foreground와 background 프로세스

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)