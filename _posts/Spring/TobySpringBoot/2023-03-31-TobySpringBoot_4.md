---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 04. 독립 실행형 스프링 애플리케이션
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_4
date: 2022-03-31 22:34:00
---
# 독립 실행형 스프링 애플리케이션
## 스프링 컨테이너 사용
![독립 실행형 스프링 애플리케이션 - 01  스프링 컨테이너 사용](https://user-images.githubusercontent.com/52024566/229534638-0b65ce10-2a73-4cc9-8b05-7024c5e0e771.png)

- 스프링 컨테이너는 애플리케이션 로직이 담긴 평범한 자바 오브젝트, 일명 POJO와 구성 정보(Configuration Metadata) 를 런타임에 조합해서 동작하는 최종 애플리케이션을 제작
- 코드로 스프링 컨테이너를 만드는 가장 간단한 방법은 컨테이너를 대표하는 인터페이스인 `ApplicationContext`를 구현한 `GenericApplicationContext`를 이용하는 것

https://docs.spring.io/spring-framework/docs/5.3.x/javadoc-api/org/springframework/context/support/GenericApplicationContext.html

- 이를 통해서 컨테이너에 등록할 빈 오브젝트 클래스 정보를 직접 등록할 수 있음. 이를 참고해서 컨테이너가 빈 오브젝트를 직접 생성

```java
GenericApplicationContext applicationContext = new GenericApplicationContext();
applicationContext.registerBean(HelloController.class);
applicationContext.refresh();
```

- 컨테이너에 필요한 정보를 등록하고 `refresh()`를 이용해서 초기화 작업을 진행
- `ApplicationContext`의 `getBean()` 메소드를 이용해서 컨테이너가 관리하는 빈 오브젝트를 가져올 수 있음. 빈의 타입(클래스, 인터페이스) 정보를 이용해서 해당 타입의 빈을 요청

```java
HelloController helloController = applicationContext.getBean(HelloController.class);
```
## 의존 오브젝트 추가
- 스프링 컨테이너는 싱글톤 패턴과 유사하게 애플리케이션이 동작하는 동안 딱 하나의 오브젝트만을 만들고 사용함. 이런 면에서 스프링 컨테이너는 싱글톤 레지스트리라고도 함
- `HelloController`가 기능을 의존해서 사용하는 `SimpleHelloService`라는 클래스를 작성. 이 클래스의 오브젝트가 필요한데 일단 이 작업은 HelloController에서 진행

```java
public String hello(String name) {
    SimpleHelloService helloService = new SimpleHelloService();
    return helloService.sayHello(Objects.requireNonNull(name))
}
```
## Dependency Injection
- 스프링 컨테이너는 DI 컨테이너. 스프링은 DI를 적극적으로 활용해서 만들어져 있고, 스프링을 이용해서 애플리케이션을 개발할 때 DI를 손쉽게 적용할 수 있도록 지원

https://martinfowler.com/articles/injection.html

- Assembler: DI에는 두 개의 오브젝트가 동적으로 의존관계를 가지는 것을 도와주는 제3의 존재
- 스프링 컨테이너는 DI를 가능하게 해주는 어셈블러로 동작

![독립 실행형 스프링 애플리케이션 - 02  Dependency Injection](https://user-images.githubusercontent.com/52024566/229536427-008c8cd7-e4a8-4771-9dd1-17674d4bc0a8.png)

![독립 실행형 스프링 애플리케이션 - 03  Dependency Injection](https://user-images.githubusercontent.com/52024566/229536433-a190314b-9508-4044-b243-a591d8afcf43.png)

- 이제 `SimpleHelloService`도 빈으로 등록을 하고 구현한 인터페이스 타입의 의존 오브젝트로 `HelloController`에 주입해서 사용되도록 함. 주입 방식은 컨트롤러의 파라미터를 이용할 수 있음