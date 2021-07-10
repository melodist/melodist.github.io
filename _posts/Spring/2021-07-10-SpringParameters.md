---
layout: post
title: 스프링 파라미터 전달 방법
tag: [Spring, UF]
permalink: /docs/Spring/Parameters
---
# 스프링 파라미터 전달 방법

## @RequestParam
1개의 HTTP 요청 파라미터를 받음

```java
@GetMapping("/searchFoo")
public List<Foo> searchFoo(@RequestParam(value="id") String id, @RequestParam(value="password") String password) {
    return fooService.searchFoo(id, password);
}
```

## @RequestBody

- 클라이언트가 전송하는 JSON 형태의 HTTP Body 내용을 Java Object로 변환

- GET 메소드는 Body가 존재하지 않기 때문에 @RequestBody 사용 시 에러 발생

- MessageConverter의 일종인 MappingJackson2HttpMessageConverter가 Body를 Java Object로 변환

```java
@PostMapping("/searchBar")
public List<Bar> searchBar(@RequestBody Foo foo) {
    return barService.searchBar(foo);
}
```
## @PathVariable

- URL에 구분자가 들어올 경우 사용

```java
@GetMapping("/user/{group}/{name}/")
public UserInfo findUserInfo(@PathVariable("group") String group, @PathVariable("name")) {
    return userService.findUserInfo(group, name);
}
```

## ArgumentResolver

- 컨트롤러의 메소드의 인자로 사용자가 임의의 값을 전달하는 방법을 제공할 때 사용
- 세션에 저장되어 있는 값 중 특정이름의 값을 메소드 인자로 전달할때 사용

### 작성 방법

1. org.springframework.web.method.support.HandlerMethodArgumentResolver를 구현한 클래스를 작성
2. supportsParameter메소드를 오버라이딩 한 후, 원하는 타입의 인자가 있는지 검사한 후 있을 경우 true가 리턴
3. resolveArgument메소드를 오버라이딩 한 후, 메소드의 인자로 전달할 값을 리턴

설정 방법

1. SpringConfig 파일에 설정
   - WebMvcConfigurerAdapter를 상속받은 Config 파일에서 addArgumentResolvers 메소드를 오버라이딩 한 후 원하는 ArgumentResolver 클래스 객체를 등록
2. XML 파일에 설정