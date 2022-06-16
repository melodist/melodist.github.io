---
layout: post
title: 스프링 핵심 원리 - 기본편  - 9. 빈 스코프
tags: [Spring, SpringCore, UF]
permalink: /docs/Spring/SpringCore_9
date: 2022-06-15 23:31:00
---

# 빈 스코프

## 빈 스코프란?

지금까지 우리는 스프링 빈이 스프링 컨테이너의 시작과 함께 생성되어서 스프링 컨테이너가 종료될 때 까지 유지된다고 학습했다. 이것은 스프링 빈이 기본적으로 싱글톤 스코프로 생성되기 때문이다. 스코프는 번역 그대로 빈이 존재할 수 있는 범위를 뜻한다.

**스프링은 다음과 같은 다양한 스코프를 지원**
- 싱글톤: 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
- 프로토타입: 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프
- **웹 관련 스코프**
  - **request**: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프
  - **session**: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프
  - **application**: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

빈 스코프는 다음과 같이 지정할 수 있음

**컴포넌트 스캔 자동 등록**
```java
@Scope("prototype")
@Component
public class HelloBean {}
```

**수동 등록**
```java
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
return new HelloBean();
}

```

## 프로토타입 스코프

싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 스프링 빈을 반환하는 반면에 프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환

**싱글톤 빈 요청**

![빈 스코프 - 01  프로토타입 스코프](https://user-images.githubusercontent.com/52024566/174089607-8b78c8c8-6f8c-4288-aca3-2830b6a3eba2.png)

1. 싱글톤 스코프의 빈을 스프링 컨테이너에 요청
2. 스프링 컨테이너는 본인이 관리하는 스프링 빈을 반환
3. 이후에 스프링 컨테이너에 같은 요청이 와도 같은 객체 인스턴스의 스프링 빈을 반환

**프로토타입 빈 요청1**

![빈 스코프 - 02  프로토타입 스코프](https://user-images.githubusercontent.com/52024566/174089610-9c8df3fc-da0c-4247-90d4-b0bedb2363ed.png)

1. 프로토타입 스코프의 빈을 스프링 컨테이너에 요청
2. 스프링 컨테이너는 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입

**프로토타입 빈 요청2**


![빈 스코프 - 03  프로토타입 스코프](https://user-images.githubusercontent.com/52024566/174089612-7aaaf9e6-362d-40f1-9ea0-33083cf8da93.png)

3. 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환
4. 이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환

**정리**
스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리한다는 것이 핵심.  클라이언트에 빈을 반환하고, 이후 스프링 컨테이너는 생성된 프로토타입 빈을 관리하지 않음
프로토타입 빈을 관리할 책임은 프로토타입 빈을 받은 클라이언트에 있으므로 `@PreDestroy` 같은 종료 메서드가 호출되지 않음

**싱글톤 스코프 빈 테스트**
```java
public class SingletonTest {
  
    @Test
    public void singletonBeanFind() {
      
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);
        SingletonBean singletonBean1 = ac.getBean(SingletonBean.class);
        SingletonBean singletonBean2 = ac.getBean(SingletonBean.class);
        System.out.println("singletonBean1 = " + singletonBean1);
        System.out.println("singletonBean2 = " + singletonBean2);
        assertThat(singletonBean1).isSameAs(singletonBean2);
        ac.close(); //종료
    }
  
    @Scope("singleton")
    static class SingletonBean {
        @PostConstruct
        public void init() {
            System.out.println("SingletonBean.init");
        }
      
        @PreDestroy
        public void destroy() {
            System.out.println("SingletonBean.destroy");
        }
    }
}
```

**실행 결과**
```
SingletonBean.init
singletonBean1 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
singletonBean2 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing SingletonBean.destroy
```

- 빈 초기화 메서드를 실행하고,
- 같은 인스턴스의 빈을 조회하고,
- 종료 메서드까지 정상 호출

**프로토타입 스코프 빈 테스트**
```java
public class PrototypeTest {
    @Test
    public void prototypeBeanFind() {
      
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);
        System.out.println("find prototypeBean1");
        PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
        System.out.println("find prototypeBean2");
        PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
        System.out.println("prototypeBean1 = " + prototypeBean1);
        System.out.println("prototypeBean2 = " + prototypeBean2);
        assertThat(prototypeBean1).isNotSameAs(prototypeBean2);
        ac.close(); //종료
    }
  
    @Scope("prototype")
    static class PrototypeBean {
      
        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init");
        }
      
        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    }
}
```

**실행 결과**
```java
find prototypeBean1
PrototypeBean.init
find prototypeBean2
PrototypeBean.init
prototypeBean1 = hello.core.scope.PrototypeTest$PrototypeBean@13d4992d
prototypeBean2 = hello.core.scope.PrototypeTest$PrototypeBean@302f7971
org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing
```

- 싱글톤 빈은 스프링 컨테이너 생성 시점에 초기화 메서드가 실행 되지만, 프로토타입 스코프의 빈은 스프링 컨테이너에서 빈을 조회할 때 생성되고, 초기화 메서드도 실행
- 프로토타입 빈을 2번 조회했으므로 완전히 다른 스프링 빈이 생성되고, 초기화도 2번 실행된 것을 확인할 수 있음
- 싱글톤 빈은 스프링 컨테이너가 관리하기 때문에 스프링 컨테이너가 종료될 때 빈의 종료 메서드가 실행되지만, 프로토타입 빈은 스프링 컨테이너가 생성과 의존관계 주입 그리고 초기화 까지만 관여하고, 더는 관리하지 않음. 따라서 프로토타입 빈은 스프링 컨테이너가 종료될 때 `@PreDestroy` 같은 종료 메서드가 전혀 실행되지 않음

**프로토타입 빈의 특징 정리**
- 스프링 컨테이너에 요청할 때 마다 새로 생성
- 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입 그리고 초기화까지만 관여
- 종료 메서드가 호출되지 않음
- 그래서 프로토타입 빈은 프로토타입 빈을 조회한 클라이언트가 관리해야 함. 종료 메서드에 대한 호출도 클라이언트가 직접 해야 함

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점
## 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 Provider로 문제 해결
## 웹 스코프
## request 스코프 예제 만들기
## 스코프와 Provider
## 스코프와 프록시