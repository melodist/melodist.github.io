---
layout: post
title: 운영체제 - 가상 메모리
tags: [OS, UF]
permalink: /docs/OS/VirtualMemory
date: 2021-03-12 13:44:00
---
# 가상 메모리의 이해
## 가상 메모리 개념

### 가상 메모리가 필요한 이유

- 하나의 프로세스만 실행 가능한 시스템 (ex. 배치 처리 시스템 등)
  1. 프로그램을 메모리에 적재
  2. 프로세스 실행
  3. 프로세스 종료 (메모리 해제)
- 여러 프로세스 동시 실행 시스템
  1. 메모리 용량 부족 이슈
  2. 프로세스 메모리 영역 간에 침범 이슈

### 가상 메모리 (Virtual Memory System)

실제 각 프로세스마다 충분한 메모리를 할당하기에는 메모리 크기에 한계가 있음

- [폰 노이만 구조](https://ko.wikipedia.org/wiki/폰_노이만_구조) 기반이므로 코드는 반드시 메모리에 있어야 함
- 프로세스가 항상 프로세스의 모든 영역을 사용하는 것은 아님

그러므로 각 프로세스에서 실제로 사용하는 부분만 메모리에 적재하면 프로세스간 공간을 분리하면서도 메모리를 효율적으로 활용할 수 있다. 이를 가상 메모리라고 한다.

**기본 아이디어**

- 프로세스는 가상 주소를 사용하고 실제 해당 주소에서 데이터를 읽고 쓸 때에만 물리 주소로 바꿔준다.
- Virtual address (가상 주소): 프로세스가 참조하는 주소
- Physical address (물리 주소): 실제 메모리 주소

#### MMU (Memory Management Unit)

CPU에서 코드 실행 시 가상 주소 메모리 접근이 필요할 때 해당 주소를 물리 주소값으로 변환해주는 장치

## 페이징 시스템

### 페이징 (paging) 개념

- 크기가 동일한 페이지로 가상 주소 공간과 이에 매칭되는 물리 주소 공간을 관리
- 하드웨어 지원이 필요 ex. Intel x86 시스템 (32bit)에서는 4KB, 2MB, 1GB 지원
- 리눅스에서는 4KB로 paging
- 페이지 번호를 기반으로 가상 주소/물리 주소 매핑 정보를 기록/사용

### 페이징 시스템 예제

- 프로세스의 PCB에 Page Table 구조체를 가리키는 주소가 들어 있음
- Page Table에는 가상 주소와 물리 주소간 매핑 정보가 있음

![VirtualMemory2](https://user-images.githubusercontent.com/52024566/110897515-eb137a80-8340-11eb-9c3d-a35e39efea0f.png)

### 페이징 시스템 구조

- page 또는 page frame: 고정된 크기의 block (4KB)
- paging system
  - 가상 주소 v = (p, d)
    - p: 가상 메모리 페이지
    - d: p 안에서 참조하는 위치
    - 페이지 크기가 4KB일 경우 0비트에서 11비트가 d를 나타내고 12비트 이상이 페이지 번호가 될 수 있음

### 페이지 테이블 (page table)

- 물리 주소에 있는 페이지 번호와 해당 페이지의 첫 물리 주소 정보를 매핑한 표
- paging system 동작
  - 특정 프로세스에서 특정 가상 주소에 접근하려면
    - 해당 프로세스의 PCB에 있는 page table에 해당 가상 주소가 포함된 page 번호가 있는지 확인
    - page 번호가 있으면 이 page가 매핑된 첫 물리 주소(p')를 알아내고 p' + d가 실제 물리 주소가 됨

#### 페이지 테이블 예제

![VirtualMemory3](https://user-images.githubusercontent.com/52024566/110899285-26637880-8344-11eb-917a-604295f88ec0.png)

**aspect의 실제 주소를 확인하는 예제**

1. Process Page Table에서 aspect가 속한 page3이 Process Page Table에 있는지 확인
2. Page3이 매핑된 첫 물리 주소인 1000h에 d = 2를 더하여 aspect의 실제 주소는 1002h

### 페이징 시스템과 MMU

- CPU는 가상 주소 접근시 하드웨어 장치인 MMU를 통해 물리 메모리에 접근
- 프로세스 생성 시 페이지 테이블 정보가 생성됨
  - PCB 등에서 해당 페이지 테이블에 접근 가능하고 관련 정보는 물리 메모리에 적재됨
  - 프로세스 구동 시 해당 페이지 테이블 base 주소가 CR3 레지스터에 저장됨
  - CPU가 가상 주소 접근 시 MMU가 페이지 테이블 base 주소에 접근하여 물리 주소를 가져옴

## References
[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)