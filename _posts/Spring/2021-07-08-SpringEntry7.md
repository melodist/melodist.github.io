---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 7. AOP
tag: [Spring, SpringEntry, UF]
permalink: /docs/Spring/Entry/7
---

# AOP (Aspect Oriented Programming)

## AOP가 필요한 상황

- 모든 메소드의 호출 시간을 측정하고 싶다면?
- 공통 관심 사항(cross-cutting concern) vs. 핵심 관심 사항(core concern)
- 회원 가입 시간, 회원 조회 시간을 측정하고 싶다면?

**MemberService 회원 조회 시간 측정 추가**

```java
package hello.hellospring.service;

@Transactional
public class MemberService {
	/**
	* 회원가입
	*/
    public Long join(Member member) {
        
        long start = System.currentTimeMillis();
        
        try {
            validateDuplicateMember(member); //중복 회원 검증
            memberRepository.save(member);
            return member.getId();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join " + timeMs + "ms");
        }
    }
    
    /**
    * 전체 회원 조회
    */
    public List<Member> findMembers() {
        
        long start = System.currentTimeMillis();
        
        try {
        	return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("findMembers " + timeMs + "ms");
        }
    }
}
```

**문제**

- 회원가입, 회원 조회에서 시간을 측정하는 기능은 핵심 관심 사항이 아니고 공통 관심 사항이다.
- 시간을 측정하는 로직과 핵심 비즈니스 로직이 섞여서 유지보수가 어렵다.
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵다.
- 시간을 측정하는 로직을 변경할 때 모든 로직을 찾아가면서 변경해야 한다.

## AOP 적용

- 공통 관심 사항과 핵심 관심 사항을 분리

![AOP 적용 01](https://user-images.githubusercontent.com/52024566/125888247-923145c2-9b93-44c1-941d-314368313d3d.png)

**시간 측정 AOP 등록**
![AOP 적용 02](https://user-images.githubusercontent.com/52024566/125888251-fc4f638b-444e-4789-b4ea-12c8974ea8b1.png)

**실행 결과**

![AOP 적용 03](https://user-images.githubusercontent.com/52024566/125888255-64b5896d-063b-485a-a65d-b9b2ed4b3225.png)

**해결**

- 회원가입, 회원 조회 등 핵심 관심 사항과 시간을 측정하는 공통 관심 사항을 분리한다.
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들어 핵심 관심 사항을 깔끔하게 유지할 수 있다.
- 변경이 필요하면 이 로직만 변경하면 되고 원하는 적용 대상을 선택할 수 있다.



## 스프링의 AOP 동작 방식 설명

**AOP 적용 전 의존관계**![AOP 적용 04](https://user-images.githubusercontent.com/52024566/125888258-a3a2c4ca-357f-4498-b2dd-3ad230818d58.png)

**AOP 적용 후 의존 관계**
![AOP 적용 05](https://user-images.githubusercontent.com/52024566/125888263-fec5e7ed-0b8f-4b74-8ab3-5e44eb369c3e.png)