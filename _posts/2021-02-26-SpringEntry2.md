---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 2. 스프링 웹 개발 기초
tag: [Spring, SpringEntry]
permalink: /docs/Spring/Entry/2/
date: 2021-02-26 13:39:00
---
# 스프링 웹 개발 기초

## 정적 컨텐츠

스프링 부트는 정적 컨텐츠를 자동으로 지원한다.

1. 웹 브라우저에서 `localhost:8080/hello-static.html` 요청
2. 스프링 부트 내장 톰캣 서버에서 스프링 컨테이너에 요청을 전달
3. 스프링 컨테이너는 hello-static 관련 컨트롤러가 있는지 검색
4. 컨트롤러가 없을 경우 `resources: static/hello-static.html` 검색

## MVC와 템플릿 엔진

MVC: Model, View, Controller

1. 웹 브라우저에서 `localhost:8080/hello-mvc` 요청
2. 스프링 부트 내장 톰캣 서버에서 스프링 컨테이너에 요청을 전달
3. `@GetMapping`에 의하여 `HelloController`의 `helloMvc()` 호출되어 hello-template 문자열 반환.
4. 반환된 문자열에 의해 `ViewResolver`가 resources/templates 폴더의 hello-template.html을 찾아서 데이터를 전달
5. Thymeleaf 템플릿 엔진이 페이지를 렌더링하여 웹 브라우저에서 표현함

![ MVC와 템플릿 엔진 01](https://user-images.githubusercontent.com/52024566/109254252-022c7580-7835-11eb-8071-f963b708ac96.png)
![MVC와 템플릿 엔진 02](https://user-images.githubusercontent.com/52024566/109254255-02c50c00-7835-11eb-8714-3c263615ce92.png)

`helloMvc()`를 통하여 Model에 데이터를 담지 않고 HTTP API를 이용하여 값을 전달할 수 있다.

## API

- `@ResponseBody`를 사용하면 `ViewResolver`를 사용하지 않고 HTTP의 BODY에 문자 내용을 직접 반환함. 하지만 이 기능은 잘 사용하지 않음.
  ![API 01](https://user-images.githubusercontent.com/52024566/109255641-c21ac200-7837-11eb-92bb-1af24660d322.png)
  ![API 02](https://user-images.githubusercontent.com/52024566/109255643-c2b35880-7837-11eb-9756-087b4589d7a7.png)
  html 페이지에 문자 내용이 그대로 입력되었음을 확인할 수 있음

- `@ResponseBody`를 사용하여 객체를 반환하면 객체가 JSON으로 반환됨
  ![06  API 04](https://user-images.githubusercontent.com/52024566/109255646-c34bef00-7837-11eb-963c-3f1ffcb79fd6.png)
  객체가 JSON으로 반환되었음을 확인할 수 있음

1. 웹 브라우저에서 `localhost:8080/hello-api` 요청
2. 스프링 부트 내장 톰캣 서버에서 스프링 컨테이너에 요청을 전달
3. `@ResponseBody`에 의하여 `HttpMessageConverter`가 동작함.
   - 기본 문자처리 : StringHttpMessageConverter
   - 기본 객체처리 : MappingJackson2HttpMessageConverter

> 참고: 클라이언트의 HTTP Accept 헤더와 서버의 컨트롤러 반환타입 정보를 조합하여 적당한 HttpMessageConverter가 선택됨


