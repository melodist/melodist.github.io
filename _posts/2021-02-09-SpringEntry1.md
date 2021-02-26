---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 1. 프로젝트 환경설정
tag: [Spring, SpringEntry]
permalink: /docs/Spring/Entry/1/
---
# 프로젝트 환경설정
## 프로젝트 생성
[https://start.spring.io](스프링 부트 스타터) 사이트 이용  
Gradle 대신 IntelliJ IDEA를 Build tool로 이용하는 것으로 실행속도를 높일 수 있다.   Intellij IDEA가 제공하는 builder는 incremental build를 지원하여 최신 상태인 클래스는 컴파일하지 않는다.

- Gradle? Maven?

Gradle과 Maven은 빌드 관리 도구로 개발자가 사용하는 라이브러리 뿐만 아니라 해당 라이브러리가 작동하는데 필요한 다른 라이브러리까지 관리한다. 그 외에도 프로젝트의 전반적인 라이프 사이클을 관리한다. Maven은 XML로 라이브러리를 정의하고 활용하지만 Gradle은 Groovy를 기반으로 한 스크립트 언어를 이용하여 필요한 항목을 설정할 수 있다.

## 라이브러리 살펴보기

### 스프링 부트 라이브러리

- spring-boot-starter-web
  - spring-boot-starter-tomcat: Web Server인 톰캣을 내장하고 있다.
  - spring-webmvc: 스프링 웹 MVC
- spring-boot-starter-thymeleaf
- spring-boot-starter
  - spring-boot
    - spring-core
  - spring-boot-starter-logging
    - logback, slf4j

### 테스트 라이브러리

* spring-boot-starter-test
  * junit: 테스트 프레임 워크
  * mockito: 테스트를 위한 객체인 mock 관련 라이브러리
    * mock? 테스트를 위하여 실제 객체를 만드는 데에 많은 비용이 들어거나 객체간 연관성이 높아 실제 객체를 구현하기 어려운 경우에 테스트에 사용하기 위한 가짜 객체.
  * assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리
  * spring-test: 스프링 통합 테스트 지원

## View 환경설정

- `static/index.html`을 올려두면 Welcome page 기능을 제공한다.

### thymeleaf 템플릿 엔진

- thymeleaf는 동적 컨텐츠를 생성하는 템플릿 엔진의 일종으로 스프링 부트가 자동 설정을 지원한다.

```java
@Controller
public class HelloController {

	@GetMapping("hello")
	public String hello(Model model) {
		model.addAttribute("data", "hello!!");
        return "hello";
    }
}
```

`resources/templates/hello.html`

```XML
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
<head>
	<title>Hello</title>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
</head>
<body>
<p th:text="'안녕하세요. ' + ${data}" >안녕하세요. 손님</p> // p th: thymeleaf 템플릿 적용시
</body>
</html>
```

### 동작 환경

1. 웹 브라우저가 `localhost:8080/hello` 요청
2. Tomcat 서버가 Spring 컨테이너에 hello가 있는지 확인
3. `HelloController`의 `hello()`가 실행되어 hello를 반환
4. 반환된 문자열 hello에 의하여 `ViewResolver`가 resources/templates 폴더의 hello.html을 찾아서 데이터를 전달
5. Thymeleaf 템플릿 엔진이 페이지를 렌더링하여 웹 브라우저에서 표현함

## 빌드하고 실행하기

1. `./gradlew build`
2. `cd build/libs`
3. `java -jar hello-spring-0.0.1-SNAPSHOT.jar`

