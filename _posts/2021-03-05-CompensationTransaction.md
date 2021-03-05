---
layout: post
title: MSA 보상 트랜잭션 Framework 구현
tags: [MSA, UF]
permalink: /docs/MSA/CompensatingTransaction
date: 2021-03-05 17:35:00
---



# MSA 보상 트랜잭션 Framework 구현

서비스1

DB1

서비스2

DB2



자기 트랜잭션이 실패했는지 어떻게 아는가

남의 트랜잭션이 실패했는지 어떻게 아는가

보상 트랜잭션을 수행할 경우 이미 Commit 해버렸는데 어떻게 Rollback 하는가

시나리오가 아니라 내부 로직을 PPT 형태로 그려볼 것

REST API의 parameter가 상황마다 다르고 URL도 상황마다 다르다

-> 이를 하나의 API로 통합하려면 어떻게 해야 할까?