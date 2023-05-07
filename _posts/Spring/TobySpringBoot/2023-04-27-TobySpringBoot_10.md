---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 10. 스프링 부트 자세히 살펴보기
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_10
date: 2022-04-27 23:29:00
---
# 스프링 부트 자세히 살펴보기
## 스프링 부트의 자동 구성과 테스트로 전환
- 직접 구현했던 자동 구성 클래스와 애노테이션 등을 제거
- 메인 애노테이션을 스프링 부트의 `@SpringBootApplication`로 변경

```java
@SpringBootApplication
public class HellobootApplication {
```

- 테스트용으로 스프링 부트가 만들어주는 내장형 DB를 이용해서 데이터 액세스 로직만 테스트 할 때는 `@JdbcTest`를 사용할 수 있음
- 프로퍼티로 설정한 DB 접속 정보가 사용되지 않으니 주의

- 스프링 컨테이너를 띄우고 자동 구성까지 적용해서 테스트를 할 때는 서블릿 컨테이너를 띄울 것인가에 따라서 다음 두 가지 방식을 사용할 수 있음
- 롤백 테스트를 위해서는 `@Transactional`을 넣어줘야 함

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.NONE)
@Transactional
public class HelloRepositoryTest {
```

- 서블릿 컨테이너를 띄워서 HTTP 요청을 보내서 테스트를 수행할 때는 다음과 같이 테스트를 준비

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.DEFINED_PORT)
public class HelloApiTest {
```

- 프로퍼티의 이름은 스프링 부트의 자동 구성에서 사용하는 것으로 변경

## 스프링 부트 자세히 살펴보기

![스프링 부트 자세히 살펴보기 - 01  스프링 부트 자세히 살펴보기](https://user-images.githubusercontent.com/52024566/235309951-13c1d7ea-fe02-41c4-af50-8a207bfb36f9.png)

- 스프링 부트의 동작 방식을 이해하고, 자신이 사용하는 기술과 관련된 자동 구성과 프로퍼티 등을 분석하고, 어떻게 활용할 수 있는지 파악하는 것이 필요

![스프링 부트 자세히 살펴보기 - 02  스프링 부트 자세히 살펴보기](https://user-images.githubusercontent.com/52024566/235309954-5f05b0f5-c9d4-4e23-ad42-89f300d6f939.png)

- 스프링 부트의 자동 구성으로부터 학습할 기술을 하나씩 찾아서 필요한 부분을 공부하는 접근 방법도 유용
## 자동 구성 분석 방법

![스프링 부트 자세히 살펴보기 - 03  자동 구성 분석 방법](https://user-images.githubusercontent.com/52024566/235427672-3c9f455c-c792-4f4a-8f0d-e290a845aec0.png)

- 자동 구성 후보 목록과 조건 판단 결과를 조회하려면 `-Ddebug`나 `--debug` 인자 값을 이용해서 스프링 부트 애플리케이션을 시작
- `ConditionEvaluationReport` 타입의 빈을 주입 받아서 필요한 정보만 선택해서 자동 구성 결과를 보는 것도 가능
- 최종적으로 등록된 빈 목록을 보고 싶으면 `ListableBeanFactory` 타입의 빈을 주입 받아서 빈 이름을 모두 가져오고, 필요하면 빈 오브젝트도 가져와서 살펴볼 수 있음
- 자동 구성 선정 결과를 기준으로 스프링 부트 레퍼런스 문서, 그리고 자동 구성 클래스의 소스 코드, 프로퍼티 클래스, 커스토마이저 등을 차근차근 따라서 살펴보면서 어떻게 어떤 조건으로 동작할지 분석

## 자동 구성 조건 결과 확인
- `ConditionEvaluationReport`를 이용해서 조건이 매칭된 자동 구성 클래스와 메소드를 출력하는 코드를 작성할 수 있음
- 그 중에서 Jmx 관련 구성 정보는 제외하고 싶으면 아래와 같이 코드를 작성

```java
@Bean ApplicationRunner run(ConditionEvaluationReport report) {
    return args -> {
        System.out.println(report.getConditionAndOutcomesBySource().entrySet().stream()
            .filter(co -> co.getValue().isFullMatch())
            .filter(co -> co.getKey().indexOf("Jmx") < 0)
            .map(co -> {
                System.out.println(co.getKey());
                co.getValue().forEach(c -> {
                System.out.println("\t" + c.getOutcome());
                });
                System.out.println();
                return co;
        }).count());
    };
}
```
## Core 자동 구성 살펴보기
- `@ConditionalOnProperty` 조건인 경우 `matchIfMissing = true`라면 프로퍼티가 존재하지 않아도 조건이 매칭

```java
@AutoConfiguration
@ConditionalOnProperty(prefix = "spring.aop", name = "auto", havingValue = "true", matchIfMissing = true)
public class AopAutoConfiguration {
```

- `SimpleCacheConfiguration`: 캐시 관련 라이브러리를 포함하지 않았을 때 사용할 수 있는 간단한 캐시
- `LifecycleAutoConfiguration`: 스프링이 제공하는 컨테이너 라이프사이클 관련 빈
- `PropertyPlaceholderAutoConfiguration`
- `TaskExecutionAutoConfiguration`
  - `ThreadPoolTaskExecutor`: `@EnableAsync`, `@EnableScheduling`에서 사용하는 `TaskExecutor` 제공
  - `TaskExecutorBuilder`: `TaskExecutor` 생성하는 Builder
## Web 자동 구성 살펴보기
- 웹 스타터를 추가하기만 해도 50개 가까운 자동 구성 클래스와 빈 메소드 조건이 추가
- `@ConditionalOnClass` 등이 붙은 클래스에 `@Import`가 붙어있는 경우엔 클래스 레벨의 조건이 모두 만족하는 경우 `@Import`가 동작해서 자동 구성을 추가

```java
@AutoConfiguration(
  after = { GsonAutoConfiguration.class, JacksonAutoConfiguration.class, JsonbAutoConfiguration.class })
@ConditionalOnClass(HttpMessageConverter.class)
@Conditional(NotReactiveWebApplicationCondition.class)
@Import({ JacksonHttpMessageConvertersConfiguration.class, GsonHttpMessageConvertersConfiguration.class, JsonbHttpMessageConvertersConfiguration.class })
public class HttpMessageConvertersAutoConfiguration {
```

- `Customizer` 빈을 이용하는 자동 구성은 프로퍼티 빈을 `Customizer`가 주입을 받고, 이를 빈 오브젝트를 만드는 메소드에서 `Customizer`를 주입 받아서 프로퍼티 설정 로직을 적용하는 방식으로 동작
- 최종 빈 오브젝트를 만드는 `Builder`를 빈으로 등록하는 경우도 있음. 이 빌더 빈을 애플리케이션에서 가져다 사용해서 빈 오브젝트를 직접 구성하는 것도 가능
- `RestTemplateBuilder`처럼 빌더만 자동 구성으로 제공하는 경우도 있음

```java
@Bean
@Lazy
@ConditionalOnMissingBean
public RestTemplateBuilder restTemplateBuilder(RestTemplateBuilderConfigurer restTemplateBuilderConfigurer) {
    RestTemplateBuilder builder = new RestTemplateBuilder();
    return restTemplateBuilderConfigurer.configure(builder);
}
```
