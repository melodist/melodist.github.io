---
layout: post
title: WebSquare
tags: [Java, Web, UF]
permalink: /docs/Websquare
date: 2021-03-25 16:50:00
---
# WebSquare

버튼 클릭 -> 이벤트 발생 -> 이벤트 함수 호출 -> submission 수행

submission은 datacollection과 연결됨. Target, Reference, Action 등 지정 가능

Submission에서 REST API 기반 요청을 보냄

submission 수행 -> URL 호출 -> WAS로 들어가 controller -> @RequestMapping(value = "url") -> Service 실행

Service 실행 -> DTO List 인자로 받아 테이블의 모든 행에 대해 반복 작업 수행

## 참고

http://docs.inswave.com:1975/