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