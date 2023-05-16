---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 11. 스프링 부트 3.0으로 예제 업그레이드
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_11
date: 2022-05-14 23:24:00
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