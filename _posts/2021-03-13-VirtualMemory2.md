---
layout: post
title: 운영체제 - 가상 메모리
tags: [OS]
permalink: /docs/OS/VirtualMemory2
date: 2021-03-13 15:45:00
---
# 가상 메모리
## 페이징 시스템
### 다중 단계 페이징 시스템

32bit 시스템에서 4KB 페이지를 위한 페이징 시스템은

- 하위 12bit는 OFFSET(변위) / 상위 20bit는 페이지 번호, 2^20(1048576)개의 페이지 정보가 필요함

페이징 정보를 단계를 나누어 생성하여 필요 없는 페이지를 생성하지 않아 공간 절약이 가능

페이지 번호를 나타내는 bit를 구분해서 단계를 나눔 (리눅스는 3단계, 최근 4단계)

![PagingSystem1](https://user-images.githubusercontent.com/52024566/111022456-35b2f680-8416-11eb-8001-f1947703678d.png)

### MMU와 TLB(컴퓨터 구조)

![PagingSystem2](https://user-images.githubusercontent.com/52024566/111022435-0f8d5680-8416-11eb-9b23-de7f1b72317d.png)

TLB(Translation Lookaside Buffer)라는 레지스터를 도입하여 MMU가 물리 주소를 확인하기 위해 메모리에 접근하는 횟수를 2번에서 1번으로 줄일 수 있다. TLB에 MMU가 찾는 물리 주소가 있을 경우 저장된 주소를 통하여 메모리에 접근한다. CR3에 있는 가상 주소를 가지고 메모리에 접근하는 과정은 TLB -> Page table -> Disk 순이다.

### 페이징 시스템과 공유 메모리

|                             Read                             |                            Write                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![PagingSystem3](https://user-images.githubusercontent.com/52024566/111022801-76ac0a80-8418-11eb-852f-77569e0e78b2.png) | ![PagingSystem4](https://user-images.githubusercontent.com/52024566/111022802-7875ce00-8418-11eb-8c85-7258846e6622.png) |

여러 프로세스가 동일한 물리 주소를 가리키는 것으로 공간과 메모리 할당 시간을 절약할 수 있다.

- Copy On Write (COW): 평소에는 리소스를 공유하다가 한 프로세스에서 리소스를 수정할 경우가 발생하면 이전 리소스의 복사본을 쓰게 한다. 이후 각 프로세서의 포인터만 변경시켜 주면 문제없이 동작한다.

## 요구 페이징 (Demand Paging / Demanded Paging)

- 프로세스의 모든 데이터를 메모리에 적재하지 않고 실행 중 필요한 시점에만 메모리로 적재하는 기법
- 프로세스의 모든 데이터를 미리 메모리에 적재하는 선행 페이징(anticipatory paging / prepaging)의 반대 개념
- 더 이상 필요하지 않은 페이지 프레임은 다시 저장매체에 저장하므로 **페이지 교체 알고리즘**이 필요

### 페이지 폴트 (page fault)

- 어떤 페이지가 실제 물리 메모리에 없을 때 일어나는 **인터럽트**로 이 인터럽트가 발생하면 운영체제는 해당 페이지를 물리 메모리에 적재

#### 페이지 폴트와 인터럽트

![PagingSystem5](https://user-images.githubusercontent.com/52024566/111023252-52056200-841b-11eb-9217-6ec668e6f541.png)

#### 생각해보기

- 페이지 폴트가 자주 일어나면 실행되기 전에 해당 페이지를 물리 메모리에 올려야 하므로 시간 소모가 큼
- 페이지 폴트가 안 일어나게 하려면 향후 실행/참조될 코드/데이터를 미리 물리 메모리에 올리면 되지만
  이러한 일은 현실적으로 불가능하다.


## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)