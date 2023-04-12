---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 06. 자동 구성 기반 애플리케이션
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_6
date: 2022-04-11 23:07:00
---
# 자동 구성 기반 애플리케이션
- `@AutoConfiguration`의 구조와 동작 원리
- Spring이 제공하는 기능을 SpringBoot가 편리하게 사용할 수 있도록 해줌

## 메타 애노테이션
- 메타 애노테이션
- 애노테이션에 적용된 애노테이션
- 기능 면에서 차이는 없지만 추가적인 정보를 획득할 수 있음
- 애노테이션의 기능을 확장
- 애노테이션을 상속하는 개념은 아님

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // Meta Annotation
public @interface Service {
}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@UnitTest
@interface FastUnitTest {
}

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.ANNOTATION_TYPE, ElementType.METHOD})
@interface UnitTest {
}
```
    
- Retention과 Target
  - Retention: 메모리 내에서 애노테이션의 유지 시점을 결정
    - SOURCE: 컴파일 할 때 버림
    - CLASS: 런타임에 버림, 기본값
    - RUNTIME: 런타임 종료 시 버림
  - Target: 애노테이션이 적용될 대상
    - TYPE: 클래스, 인터페이스
    - FIELD: 상수 포함 객체 필드
    - METHOD: 메소드
    - PARAMETER: 파라미터에 선언
## 합성 애노테이션
- 하나 이상의 메타 애노테이션이 적용된 애노테이션
- 모든 메타 애노테이션이 적용된 것과 동일한 효과
  - `@RestController` = `@Component` \+ `@ResponseBody`

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration
@ComponentScan
public @interface MySpringBootApplication {
}
```

```java
@Configuration
public class Config {
    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

        @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }
}
```
## 빈 오브젝트의 역할과 구분

![자동 구성 기반 애플리케이션 - 01  빈 오브젝트의 역할과 구분](https://user-images.githubusercontent.com/52024566/231475000-0b467275-aa82-44e2-a5aa-92f9e9bfd2d6.png)

### 애플리케이션 로직 빈
- 애플리케이션의 비즈니스 로직을 담고 있는 클래스로 만들어지는 빈
- 컴포넌트 스캐너에 의해서 빈 구성 정보가 생성되고 빈 오브젝트로 등록됨
### 애플리케이션 인프라스트럭처 빈
- 빈 구성 정보에 의해 컨테이너에 등록되는 빈이지만 애플리케이션의 로직이 아니라 애플리케이 션이 동작하는데 꼭 필요한 기술 기반을 제공하는 빈
- 전통적인 스프령 애플리케이션에서는 빈으로 등록되지 않지만 스프링 부트에서 구성 정보에 의 해 빈으로 등록되어지는 `ServletWebServerFactory`나 `DispatcherServlet` 등도 애플리케이션 인프라 빈이라고 볼 수 있음
### 컨테이너 인프라스트럭처 빈
- 스프링 컨테이너의 기능을 확장해서 빈의 등록과 생성, 관계설정, 초기화 등의 작업에 참여하는 빈을 컨테이너 인프라스트럭처 빈, 줄여서 컨테이너 인프라 빈이라고 함
- 개발자가 작성한 구성 정보에 의해서 생성되는 게 아니라 컨테이너가 직접 만들고 사용하는 빈이기 때문에 애플리케이션 빈과 구분
- 필요한 경우 일부 컨테이너 인프라 빈은 주입 받아서 활용할 수 있음
    ![자동 구성 기반 애플리케이션 - 02  빈 오브젝트의 역할과 구분](https://user-images.githubusercontent.com/52024566/231475009-9a0206e7-142e-4fc4-ac6f-b70ba0842d0d.png)