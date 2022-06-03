---
layout: post
title: 스프링 핵심 원리 - 기본편  - 5. 싱글톤 컨테이너
tags: [Spring, SpringCore, UF]
permalink: /docs/Spring/SpringCore_5
date: 2022-06-03 23:26:00
---

# 싱글톤 컨테이너

## 웹 애플리케이션과 싱글톤

- 스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생
- 대부분의 스프링 애플리케이션은 웹 애플리케이션. 물론 웹이 아닌 애플리케이션 개발도 얼마든지 개발 가능
- 웹 애플리케이션은 보통 여러 고객이 동시에 요청

![싱글톤 컨테이너 - 01  웹 애플리케이션과 싱글톤](https://user-images.githubusercontent.com/52024566/171875700-82e7dfeb-b1a6-4a26-9f0a-6f30e431b304.png)

**스프링 없는 순수한 DI 컨테이너 테스트**
```java
public class SingletonTest {
@Test
@DisplayName("스프링 없는 순수한 DI 컨테이너")
  
    void pureContainer() {
      
        AppConfig appConfig = new AppConfig();
        //1. 조회: 호출할 때 마다 객체를 생성
        MemberService memberService1 = appConfig.memberService();
      
        //2. 조회: 호출할 때 마다 객체를 생성
        MemberService memberService2 = appConfig.memberService();
      
        //참조값이 다른 것을 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);
      
        //memberService1 != memberService2
        assertThat(memberService1).isNotSameAs(memberService2);
    }
}
```

- 우리가 만들었던 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청을 할 때 마다 객체를 새로 생성
- 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸 → 메모리 낭비가 심함
- 해당 객체가 딱 1개만 생성되고, 공유하도록 설계 → 싱글톤 패턴

## 싱글톤 패턴

- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
- 그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 함
  - private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 함

**싱글톤 패턴을 적용한 예제**
```java
public class SingletonService {
  
    //1. static 영역에 객체를 딱 1개만 생성해둔다.
    private static final SingletonService instance = new SingletonService();
  
    //2. public으로 열어서 객체 인스터스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
    public static SingletonService getInstance() {
      return instance;
    }
  
    //3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
    private SingletonService() {
    }
  
    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```

1. static 영역에 객체 instance를 미리 하나 생성해서 적재
2. 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있음. 이 메서드를 호출하면 항상 같은 인스턴스를 반환
3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 `private`으로 막아서 혹시라도 외부에서 `new` 키워드로 객체 인스턴스가 생성되는 것을 방지

**싱글톤 패턴을 사용하는 테스트 코드**
```java
@Test
@DisplayName("싱글톤 패턴을 적용한 객체 사용")
public void singletonServiceTest() {
  
    //private으로 생성자를 막아두었다. 컴파일 오류가 발생한다.
    //new SingletonService();
  
    //1. 조회: 호출할 때 마다 같은 객체를 반환
    SingletonService singletonService1 = SingletonService.getInstance();
    //2. 조회: 호출할 때 마다 같은 객체를 반환
    SingletonService singletonService2 = SingletonService.getInstance();
  
    //참조값이 같은 것을 확인
    System.out.println("singletonService1 = " + singletonService1);
    System.out.println("singletonService2 = " + singletonService2);
  
    // singletonService1 == singletonService2
    assertThat(singletonService1).isSameAs(singletonService2);
    singletonService1.logic();
}
```

- private으로 new 키워드를 막아둠
- 호출할 때 마다 같은 객체 인스턴스를 반환하는 것을 확인
> 참고: 싱글톤 패턴을 구현하는 방법은 여러 가지가 있으나 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택

싱글톤 패턴을 적용하면 고객의 요청이 올 때 마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있음. 하지만 싱글톤 패턴은 다음과 같은 수 많은 문제점들을 가지고 있음

**싱글톤 패턴 문제점**
- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어감
- 의존관계상 클라이언트가 구체 클래스에 의존하므로 DIP를 위반
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높음
- 테스트하기 어려움
- 내부 속성을 변경하거나 초기화 하기 어려움
- private 생성자로 자식 클래스를 만들기 어려움
- 결론적으로 유연성이 떨어져 안티패턴으로 불리기도 함

## 싱글톤 컨테이너

![싱글톤 컨테이너 - 02  싱글톤 컨테이너](https://user-images.githubusercontent.com/52024566/171875705-2891423c-cb7d-4cf9-93ac-3b68d6126904.png "싱글톤 컨테이너 - 02  싱글톤 컨테이너")
![싱글톤 컨테이너 - 03  @Configuration](https://user-images.githubusercontent.com/52024566/171875708-b34dc152-74b5-4616-a85e-60d58ce1d197.png)
## 싱글톤 방식의 주의점
## @Configuration과 싱글톤
## @Configuration과 바이트코드 조작의 마법