---
layout: post
title: 소켓 프로그래밍
tag: [Network, Socket]
permalink: /docs/Network/SocketProgramming
---

# 소켓 프로그래밍

## 소켓(Socket)?

데이터를 주고받을 수 있게 해주는 구조체

## 소켓 API 실행 흐름

![소켓API](https://user-images.githubusercontent.com/52024566/109390864-6e9f9580-7957-11eb-8515-fccf7469f06c.PNG)

1. socket(네트워크 주소 체계, 소켓 타입, 프로토콜)

   소켓을 생성하는 함수로 소켓 생성에 실패할 경우 -1을 반환.

   - 네트워크 주소 체계: IPv4(AF_INET), IPv6(AF_INET6)
   - 소켓 타입: TCP(SOCK_STREAM), UDP(SOCK_DGRAM)\
   - 프로토콜: TCP(IPPROPTO_TCP), UDP(IPPROTO_UDP)

2. bind(소켓 변수, 서버 주소 구조체, 서버 주소 구조체의 크기)
3. listen()
4. connect()
5. accept()
6. send()
7. recv()
8. closesocket()

## Winsock2

- WSAStartup()
- WSACleanup()