---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 02. 스프링 부트 개발 시작하기
tags: [Spring, TobySpringBoot]
permalink: /docs/Spring/TobySpringBoot_2
date: 2023-03-26 23:18:00
---
# 스프링 부트 개발 시작하기
## HelloBoot 웹 프로젝트 생성
### 스프링 부트 프로젝트 생성
- 웹 Spring Initializr - https://start.spring.io/
- IDE의 Spring Initializr 프로젝트 생성 메뉴
- Spring Boot CLI
### 생성 옵션
- Project: Gradle
- Langauge: Java
- SpringBoot Version: 2.7.6
- Group Id: tobyspring
- Name: hello
- Packaging: Jar
- Java Version: 11
- Dependency: Web
## Hello 컨트롤러
```java
@RestController
public class HelloController {
  
    @GetMapping("/hello")
    public String hello(String name) {
        return "Hello " + name;
    }
}
```
## Hello API 테스트
`hello()` 컨트롤러 메소드를 호출할 수 있는 HTTP Request를 생성하고 리턴되는 HTTP Response를 확인
## HTTP 요청을 만들고 응답을 확인하는데 사용되는 도구
- 웹 브라우저 개발자 도구
- curl
- HTTPie
- Intellij IDEA Ultimate- http request
- Postman API Platform
- JUnit Test
- 각종 API 테스트 도구
## HTTP
웹 Request와 Response의 기본 구조를 이해하고 내용을 확인할 수 있어야 함
### Request
- Request Line: Method, Path, HTTP Version
- Headers
- Message Body
### Response
- Status Line: HTTP Version, Status Code, Status Text
- Headers
- Message Body

```
http -v ":8080/hello?name=Spring"
```
```
GET /hello?name=Spring HTTP/1.1
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
Host: localhost:8080
User-Agent: HTTPie/3.2.1

HTTP/1.1 200
Connection: keep-alive
Content-Length: 12
Content-Type: text/plain;charset=UTF-8
Date: Thu, 01 Dec 2022 01:45:15 GMT
Keep-Alive: timeout=60
Hello Spring
```
