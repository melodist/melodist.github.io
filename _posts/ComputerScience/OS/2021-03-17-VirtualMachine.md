---
layout: post
title: 운영체제 - 가상 머신의 이해
tags: [OS]
permalink: /docs/OS/VirtualMachine
date: 2021-03-17 09:17:00
---
# 가상 머신의 이해

## Virtual Machine (가상 머신)

- 하나의 하드웨어 (CPU, Memory 등)에 다수의 운영체제를 설치하고, 개별 컴퓨터처럼 동작하도록 하는 프로그램

![VirtualMachine1](https://user-images.githubusercontent.com/52024566/111403586-955c1b00-8710-11eb-817f-92f65b90a6a7.png)

### Virtual Machine Type1 (native / bare metal)

- 하이퍼 바이저 (VMM): 운영체제와 응용프로그램을 물리적 하드웨어에서 분리하는 프로세스
- 하이퍼 바이저 또는 버추얼 모신 모니터 (VMM)라고 하는 소프트웨어가 하드웨어에서 직접 구동
- ex. Xen, KVM

![VirtualMachine2](https://user-images.githubusercontent.com/52024566/111403591-9725de80-8710-11eb-875c-a6f5c78cb7cb.png)

### Virtual Machine Type2

- 하이퍼 바이저 또는 VMM이 Host OS 상위에 설치
- ex. VMWare, Parallels Desktop (Mac)

![VirtualMachine3](https://user-images.githubusercontent.com/52024566/111403593-9725de80-8710-11eb-9e52-e0da47fecaa2.png)

###  Full Virtualization (전가상화) vs. Half Virtualization (반가상화)
|Full Virtualization|Half Virtualization|
|:-:|:-:|
|![VirtualMachine4](https://user-images.githubusercontent.com/52024566/111403596-97be7500-8710-11eb-8d92-68c13fcbc545.png)|![VirtualMachine5](https://user-images.githubusercontent.com/52024566/111403599-97be7500-8710-11eb-9628-8f6dc8862a15.png)|
|각 가상머신이 하이퍼바이저를 통해서 하드웨어와 통신<br />하이퍼바이저가 마치 하드웨어인 것처럼 동작하므로, 가상머신의 OS는 자신이 가상머신인지 모름|각 가상머신이 직접 하드웨어와 통신<br />각 가상머신의 OS는 기존 OS에 하이퍼바이저 명령을 추가하여 하드웨어와 통신|
> 최근 H/W 성능 개선으로 전가상화를 선호

### KVM

- AWS 등에서 사용하는 Type1 형태의 가상머신

![VirtualMachine6](https://user-images.githubusercontent.com/52024566/111403600-98570b80-8710-11eb-95e3-a32254413d5b.png)

### 또 다른 가상 머신: Docker

![VirtualMachine7](https://user-images.githubusercontent.com/52024566/111403602-98570b80-8710-11eb-8d3c-621fa2069a59.png)

- 가상머신은 컴퓨터 하드웨어를 가상화 (하드웨어 전체 추상화)
  - 하이퍼바이저 사용, 추가 OS 필요 등 성능 저하 이슈 존재
- Docker는 운영체제 레벨에서 별도로 분리된 실행환경을 제공 (커널 추상화)
  - LINUX를 처음 설치했을 때와 유사한 실행환경을 만들어주는 LINUX 컨테이너 기술 기반
  - LINUX 컨테이너 기술이므로 macOS나 windows에 설치할 경우에는 가상머신 기반

### Java Virtual Machine

![VirtualMachine8](https://user-images.githubusercontent.com/52024566/111403604-98efa200-8710-11eb-90c7-6f3760fe38b7.png)

- 가상머신과는 다른 목적 (응용 프로그램 레벨 가상화)
- Java 컴파일러는 CPU 의존성을 가지지 않는 bytecode를 생성하고 이 파일을 JVM이 실행
- 각 운영체제에 맞는 JVM 프로그램 존재

### 가상머신 정리

- 하드웨어에 직접 접근하는 Bare-Metal 방식이 가장 좋음. AWS 환경도 Bare-Metal 기반 KVM
- Docker는 경량 이미지로 실행환경을 통째로 백업 및 실행 가능하여 실무에 많이 사용됨
  - Data Engineering에서 Docker로 시스템 환경 설정 + 프로그램을 한 번에 배포
  - ex. 프로그램 업데이트 -> Docker 이미지 작성 -> Jenkins로 배치 Job 생성 및 실행 (AWS EC2 재생성 및 Docker 이미지 설치, 실행)

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)