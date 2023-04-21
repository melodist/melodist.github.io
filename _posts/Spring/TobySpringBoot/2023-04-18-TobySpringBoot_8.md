---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 08. 외부 설정을 활용하는 자동 구성
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_8
date: 2022-04-19 23:05:00
---
# 외부 설정을 활용하는 자동 구성
## 스프링의 Environment 추상화
![외부 설정을 활용하는 자동 구성 - 01  스프링의 Environment 추상화](https://user-images.githubusercontent.com/52024566/233390547-c25e4cb2-c583-48b1-8cc2-915dbfded3d1.png)

- 스프링의 `Environment` 추상화는 애플리케이션의 두 가지 환경 정보 모델인 `profile`과 `properties`를 제공
- 자동 구성 정보의 일부 내용을 변경하거나 설정해야 할 때 `Environment`를 통해서 프로퍼티 값을 가져와 활용할 수 있음
- 커스톰 빈 등록을 하는 방법에 비해서 간단하게 자동 구성의 디폴트 설정을 변경하는 게 가능
- 프로퍼티 정보는 시스템 프로퍼티, 환경 변수, 서블릿 파라미터, JNDI 등에서 우선순위에 따라서 가져옴
- 애플리케이션 코드에서 `@PropertySource`로 프로퍼티 값을 가져올 대상을 지정할 수 있음
- 스프링 부트는 기본적으로 `application.properties`, `application.xml`, `application.yml` 등의 파일에서 프로퍼티를 읽어오는 기능을 추가
## 자동 구성에 Environment 프로퍼티 적용
스프링 부트의 모든 애플리케이션 초기화 작업이 끝나고 나면 실행되는 코드를 만들 때 ApplicationRunner 인터페이스를 구현한 오브젝트 또는 람다식을 빈으로 등록

```java
@Bean
ApplicationRunner applicationRunner(Environment environment) {
    return args -> {
        String name = environment.getProperty("my.name");
        System.out.println("my.name: " + name);
    };
}
```

자동 구성 클래스의 메소드에도 `Environment`를 주입 받아서 빈 속성으로 지정할 프로퍼티 값을 가져올 수 있음

```java
@Bean("tomcatWebServerFactory")
@ConditionalOnMissingBean
public ServletWebServerFactory servletWebServerFactory(Environment env) {
    TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
    factory.setContextPath(env.getProperty("contextPath"));
    return factory;
}
```
## @Value와 PropertySourcesPlaceholderConfigurer
- `@Value` 애노테이션은 엘리먼트로 치환자(placeholder)를 지정하고 컨테이너 초기화시 프로퍼티 값으로 이를 대체할 수 있음
- `@Value`의 치환자를 프로퍼티 값으로 교체하려면 `PropertySourcesPlaceholderConfigurer` 타입의 빈을 등록해줘야 함
- `PropertySourcesPlaceholderConfigurer`는 빈 팩토리의 후처리기로 동작해서 초기 구성 정보에서 치환자를 찾아서 교체하는 기능을 담당
- `PropertySourcesPlaceholderConfigurer`도 자동 구성 빈으로 등록

```java
@MyAutoConfiguration
public class PropertyPlaceholderConfig {
    @Bean PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
        return new PropertySourcesPlaceholderConfigurer();
    }
}
```
## 프로퍼티 클래스의 분리
- 자동 구성에 적용할 프로퍼티의 갯수가 많아지고 프로퍼티를 처리할 로직이 추가되야 한다면, 프로퍼티를 다루는 기능을 별도의 클래스로 분리하는 것이 좋음
- 기본적인 프로퍼티 클래스는 프로퍼티 값을 가지고 있는 단순한 클래스로 작성할 수 있음

```java
public class ServerProperties {
  
    private String contextPath;
    private int port;
  
    public String getContextPath() {
        return contextPath;
    }
  
    public void setContextPath(String contextPath) {
        this.contextPath = contextPath;
    }
  
    public int getPort() {
        return port;
    }
  
    public void setPort(int port) {
        this.port = port;
    }
}
```

- 이 클래스를 빈으로 등록하는 자동 구성 클래스를 추가
- `Environment`에서 프로퍼티 값을 가져와 오브젝트에 주입하는 것은 스프링 부트의 `Binder` 클래스를 이용하면 편리

```java
@MyAutoConfiguration
public class ServerPropertiesConfig {
    @Bean
    public ServerProperties serverProperties(Environment environment) {
        return Binder.get(environment).bind("", ServerProperties.class).get();
    }
}
```

- 프로퍼티 클래스로 만든 빈은 자동 구성 빈을 만들 때 주입 받아서 사용

```java
@Bean("tomcatWebServerFactory")
@ConditionalOnMissingBean
public ServletWebServerFactory servletWebServerFactory(ServerProperties properties) {
    TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
  
    factory.setContextPath(properties.getContextPath());
    factory.setPort(properties.getPort());
  
    return factory;
}
```