---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 11. 스프링 부트 3.0으로 예제 업그레이드
tags: [Spring, TobySpringBoot]
permalink: docs Spring TobySpringBoot_11
date: 2023-05-14 23:24:00
---
# 스프링 부트 3.0으로 예제 업그레이드
## 스프링 부트 3.0
- Spring 6
- JDK 17 or higher
- Jakarta EE 9, 10
## 2.7 예제 3.0으로 업그레이드
- build.gradle의 스프링 부트 버전 수정
- Jakarta EE의 패키지명으로 변경
- SpringMVC의 바뀐 동작 방식 적용
  - 타입 레벨 단독 `@RequestMapping` 문제

### build.gradle
```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.0.1'
    id 'io.spring.dependency-management' version '1.1.0'
}

group = 'tobyspring'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '17'
```

### 타입 레벨 @RequestMapping 문제
- Spring 6는 타입 레벨에 단독으로 존재하는 `@RequestMapping`을 `DispatcherServlet`이 인식하지 못함. `@Controller`까지 지정해줘야 함