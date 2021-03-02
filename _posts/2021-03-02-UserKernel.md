---
layout: post
title: 운영체제 - 사용자 모드와 커널 모드
tag: [OS]
permalink: /docs/OS/UserKernel
---

## CPU Protection Rings

![CPU Protection Rings](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2f/Priv_rings.svg/1200px-Priv_rings.svg.png)

위의 그림은 x86 아키텍쳐를 기준으로 하였음

- 사용자 모드 (User mode)
  - 응용 프로그램이 CPU를 사용할 때
  - 하드웨어에 직접 접근할 수 없음
- 커널 모드 (Kernel mode)
  - OS가 CPU를 사용할 때
  - 모든 시스템 메모리와 모든 CPU 명령어에 접근할 수 있음

사용자 모드와 커널 모드로 구분하여 한 응용 프로그램이 시스템 전체의 안정성을 해치지 않게 보장함

## 예제

```c
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main() {
    int fd;
    fd = open("data.txt".O_RDONLY);
    if(fd == =1)
    {
        print("Error: can not open file\n");
        /* 파일을 열지 못했으므로 그냥 종료*/
        return -1;
    }
    else
    {
        print("File opened and now close_\n");
        close(fd);
        return 0;
    }
}
```

1. 사용자 모드에서 프로그램 실행
2. open() 시스템 콜 호출
3. 커널 모드로 전환
4. open() 함수를 처리하는 sys_open() 커널 함수 호출
5. Low level에서 파일 열기 연산 수행
6. 사용자 모드로 전환
7. open() 함수 이후의 프로그램 실행

## References

[컴퓨터 공학 전공 필수 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_cs)