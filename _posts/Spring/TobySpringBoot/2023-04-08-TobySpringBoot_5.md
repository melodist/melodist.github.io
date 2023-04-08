---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 05. DI와 테스트, 애노테이션 활용
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_5
date: 2022-04-08 12:21:00
---
# DI와 테스트, 애노테이션 활용
## 테스트 코드를 이용한 테스트
### TestRestTemplate
- 번거로운 수동 테스트 대신 자동화된 테스트 코드를 이용한 테스트 방식으로 전환
- 웹 서버에 HTTP 요청을 보내고 응답을 받아서 검증하는 테스트에서는 `TestRestTemplate`
을 사용하면 편리

```java
@Test
void hello() {
    TestRestTemplate restTemplate = new TestRestTemplate();
  
    ResponseEntity<String> res = restTemplate.getForEntity("http://localhost:8080/hello?name={name}", String.class, "Spring");
  
    assertThat(res.getStatusCode()).isEqualTo(HttpStatus.OK);
    assertThat(res.getHeaders().getFirst(HttpHeaders.CONTENT_TYPE)
               .startsWith(MediaType.TEXT_PLAIN_VALUE)).isTrue();
    assertThat(res.getBody().trim()).isEqualTo("Hello Spring");
}
```

- HTTP 응답의 3가지 요소인 상태코드, 헤더(Content-Type), 바디를 검증
## DI와 단위 테스트
- DI는 컨테이너 없이도 자바 코드로 활용이 가능
- 테스트 코드를 작성할 때 이용하면 의존관계 주입을 이용해서 고립된 테스트를 작성할 수 있음
- 의존 오브젝트가 없는 코드는 단순하게 인스턴스를 만들어서 메소드를 호출하는 방법으로 테스트 할 수 있음

```java
@Test
void simpleHelloService() {
    SimpleHelloService helloService = new SimpleHelloService();
  
    String ret = helloService.sayHello("Test");
  
    Assertions.assertThat(ret).isEqualTo("Hello Test");
}
```

- 의존 오브젝트가 있는 경우, 테스트가 실행되는 동안에 수행될 최소한의 기능을 가진 의존 오브젝트 코드를 테스트용으로 만들어서 사용할 수 있음
- 의존 오브젝트의 인터페이스를 구현한 간단한 클래스 또는 익명 클래스, 혹은 인터페이스가 하나라면 람다식을 사용할 수 있음

```java
@Test
void helloController() {
    HelloController helloController = new HelloController(name -> name);
  
    String ret = helloController.hello("Test");
  
    Assertions.assertThat(ret).isEqualTo("Test");
}
```

- 예외가 발생하는, 실패한 경우에 대한 테스트도 작성해야 함