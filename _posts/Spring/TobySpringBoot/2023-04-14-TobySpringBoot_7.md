---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 07. 조건부 자동 구성
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_7
date: 2022-04-14 22:36:00
---
# 조건부 자동 구성
## 스타터와 Jetty 서버 구성 추가
- 스프링 부트의 Starter는 애플리케이션에 포함시킬 의존 라이브러리 정보를 담고 있음
- Maven 또는 Gradle의 의존 라이브러리 목록에 추가해서 스프링 부트가 선정한 기술의 종류와 버전에 해당하는 라이브러리 모듈을 프로젝트에 포함
### spring-boot-starter
- 스프링 코어, 스프링 부트 코어를 포함해서 자동 구성, 애노테이션, 로깅 등에 필요한 기본적인 의존 라이브러리가 포함됨

![조건부 자동 구성 - 01  스타터와 Jetty 서버 구성 추가](https://user-images.githubusercontent.com/52024566/232228730-0e0201f7-ec02-468d-9ad5-bcc0b10abfc8.png)
### spring-boot-start-web
- Spring Initializr에서 web 모듈을 선택하면 이 스타터가 추가됨
- spring-boot-starter를 포함
- SpringWeb, SpringMVC와 Json, Tomcat 라이브러리가 추가됨

![조건부 자동 구성 - 02  스타터와 Jetty 서버 구성 추가](https://user-images.githubusercontent.com/52024566/232228734-2612b4b3-b23c-419a-b5c4-e40691c6df9e.png)
### spring-boot-starter-jetty
- Jetty 서블릿 컨테이너를 이용하는데 필요한 라이브러리로 구성됨
## @Conditional과 Condition
- `@Conditional` 스프링 4.0에 추가된 애노테이션으로 모든 조건을 만족하는 경우에만 컨테이너에 빈으로 등록되도록 함

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    /**
    * All {@link Condition} classes that must {@linkplain Condition#matches match}
    * in order for the component to be registered.
    */
    Class<? extends Condition>[] value();
}
```

- `Condition`은 `@Conditional`에 지정되어서 구체적인 매칭 조건을 가진 클래스가 구현해야할 인터페이스

```java
@FunctionalInterface
public interface Condition {
    /**
    * Determine if the condition matches.
    */
    boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
```

- `@Conditional`은 `@Configuration` 클래스와 `@Bean` 메소드에 적용 가능
- 클래스 조건을 만족하지 못하는 경우 메소드는 무시

![조건부 자동 구성 - 03  @Conditional과 Condition](https://user-images.githubusercontent.com/52024566/232277871-088f19a2-0b43-4356-9dc6-60be23943271.png)

- 스프링 부트가 제공하는 `ApplicationContextRunner`를 사용하면 스프링 컨테이너에 빈이 등록됐는지를 테스트 할 때 편리
- `@Conditional`이 적용된 자동 구성 클래스의 적용 여부를 테스트 할 때 사용

```java
ApplicationContextRunner contextRunner = new ApplicationContextRunner();
contextRunner.withUserConfiguration(Config1.class)
  .run(context -> {
    assertThat(context).hasSingleBean(MyBean.class);
    assertThat(context).hasSingleBean(Config1.class);
  });
```

```java
new ApplicationContextRunner().withUserConfiguration(Config2.class)
  .run(context -> {
    assertThat(context).doesNotHaveBean(MyBean.class);
    assertThat(context).doesNotHaveBean(Config1.class);
  });
```

- `Condition`의 `matches` 메소드는 `@Conditional` 애노테이션의 엘리먼트 정보를 가져올 수 있는 `AnnotatedTypeMetadata`를 전달받음

```java
class BooleanCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Map<String, Object> annotationAttributes = metadata.getAnnotationAttributes(BooleanConditional.class.getName());
        Boolean value = (Boolean)annotationAttributes.get("value");
        return value;
    }
}
```
## 커스톰 @Conditional
### 클래스 기준 조건부 구성
- 스프링 부트가 사용하는 `@Conditional`의 가장 대표적인 방법은 클래스의 존재를 확인하는 것
- 스타터를 이용하거나 직접 의존 라이브러리 등록을 통해서 어떤 기술의 클래스를 애플리케이션이 사용하도록 포함시켰다면, 이 기술을 사용할 의도가 있다는 것으로 보고 관련 자동 구성 클래스를 등록
- Tomcat과 Jetty 중에서 어떤 서블릿 컨테이너를 사용할지는 해당 서버 라이브러리 클래스가 프로젝트에 포함되어있는지를 확인하는 방법을 사용
- 특정 클래스가 현재 프로젝트에 포함되어서 클래스패스에 존재하는지 확인할 때는 스프링 `ClassUtils.isPresent()`를 사용

```java
public class MyOnClassCondition implements Condition {
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        Map<String, Object> attrs = metadata.getAnnotationAttributes(ConditionalMyOnClass.class.getName());
        String value = (String) attrs.get("value");
        return ClassUtils.isPresent(value, context.getClassLoader());
    }
}
```

커스톰 @Conditional을 사용할 때의 동작 방식

![조건부 자동 구성 - 04  커스톰 @Conditional](https://user-images.githubusercontent.com/52024566/232524038-5fc88826-4882-4b83-ac1f-83c858b4c505.png)