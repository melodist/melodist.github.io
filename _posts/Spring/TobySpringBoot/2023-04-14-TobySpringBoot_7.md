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