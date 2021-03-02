---
layout: post
title: 운영체제 - 시스템 콜
tag: [OS]
permalink: /docs/OS/SystemCall
---

# 운영체제 - 시스템 콜

## 응용 프로그램, 운영체제, 컴퓨터 하드웨어 관계

- 운영체제는 응용 프로그램이 요청하는 메모리를 허가하고 분배한다.
- 운영체제는 응용 프로그램이 요청하는 CPU 시간을 제공한다.
- 운영체제는 응용 프로그램이 요청하는 I/O Device 사용을 허가 및 제어한다.

## 쉘 (Shell)

- 사용자가 운영체제 기능과 서비스를 조작할 수 있도록 인터페이스를 제공하는 프로그램
- CLI(Character Line Interface)와 GUI(Graphic User Interface) 환경으로 분류

## API(Application Programming Interface)

- 응용 프로그램이 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스

> 인터페이스(Interface): 인터페이스는 컴퓨터 시스템끼리 정보를 교환하는 공유 경계를 의미한다, 터치 스크린과 같은 일부 컴퓨터 하드웨어 장치들은 인터페이스를 통해 데이터를 송수신할 수 있으며, 마우스나 마이크 같은 장치들은 오직 시스템에 데이터를 전송만 하는 인터페이스를 제공한다.

- 소프트웨어 컴포넌트의 기능, 입력, 출력, 그리고 이에 사용되는 자료형으로 표현
- API는 사양(Specification)만을 정의한 것이고 구현(Implementation)체인 라이브러리와 함께 제공할 경우 이를 SDK(Software Development Kit)라고 함

## 시스템 콜

![운영체제](https://media.vlpt.us/images/oen/post/177c1574-1f0d-4083-b3fe-7378ca26b593/image.png)

- 응용 프로그램이 운영체제의 각 기능을 사용할 수 있도록 제공하는 함수
  - 일반적인 응용 프로그램은 사용자 모드에서 실행되므로 커널 모드에 직접적인 접근이 불가능
  - 따라서, 시스템 콜이 커널에 요청하여 커널 모드에서 처리한 결과를 사용자 모드의 프로그램에 전달
- 응용 프로그램이 대부분의 경우 시스템 콜을 직접 호출하지 않고 API를 통하여 시스템 콜을 호출


## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)