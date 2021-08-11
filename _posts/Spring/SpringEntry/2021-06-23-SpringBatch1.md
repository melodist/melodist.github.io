---
layout: post
title: 스프링 배치
tag: [Spring, SpringBatch, UF]
permalink: /docs/Spring/SpringBatch
---

Builder 메소드

Factory 메소드

Job

- get()
- incrementer
- listener
- start
- next
- build

Job Instance : Job Paramter 단위로 생성

Job Execution : Job Instance의 n번째 시도

Spring Batch 메타 데이터

- 이전에 실행한 Job이 어떤 것들이 있는지
- 최근 실패한 Batch Parameter가 어떤 것들이 있고, 성공한 Job은 어떤 것들이 있는지
- 다시 실행한다면 어디서부터 시작하면 될지
- 어떤 Job에 어떤 Step들이 있었고, Step들 중 성공한 Step과 실패한 Step들은 어떤 것들이 있는지

@Component와 @StepScope의 차이