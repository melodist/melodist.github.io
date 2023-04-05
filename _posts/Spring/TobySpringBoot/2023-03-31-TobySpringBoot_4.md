---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 04. 독립 실행형 스프링 애플리케이션
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_4
date: 2022-03-31 22:34:00
---
# 독립 실행형 스프링 애플리케이션
## 스프링 컨테이너 사용
![독립 실행형 스프링 애플리케이션 - 01  스프링 컨테이너 사용](https://user-images.githubusercontent.com/52024566/229534638-0b65ce10-2a73-4cc9-8b05-7024c5e0e771.png)

- 스프링 컨테이너는 애플리케이션 로직이 담긴 평범한 자바 오브젝트, 일명 POJO와 구성 정보(Configuration Metadata) 를 런타임에 조합해서 동작하는 최종 애플리케이션을 제작
- 코드로 스프링 컨테이너를 만드는 가장 간단한 방법은 컨테이너를 대표하는 인터페이스인 `ApplicationContext`를 구현한 `GenericApplicationContext`를 이용하는 것

https://docs.spring.io/spring-framework/docs/5.3.x/javadoc-api/org/springframework/context/support/GenericApplicationContext.html

- 이를 통해서 컨테이너에 등록할 빈 오브젝트 클래스 정보를 직접 등록할 수 있음. 이를 참고해서 컨테이너가 빈 오브젝트를 직접 생성

```java
GenericApplicationContext applicationContext = new GenericApplicationContext();
applicationContext.registerBean(HelloController.class);
applicationContext.refresh();
```

- 컨테이너에 필요한 정보를 등록하고 `refresh()`를 이용해서 초기화 작업을 진행
- `ApplicationContext`의 `getBean()` 메소드를 이용해서 컨테이너가 관리하는 빈 오브젝트를 가져올 수 있음. 빈의 타입(클래스, 인터페이스) 정보를 이용해서 해당 타입의 빈을 요청

```java
HelloController helloController = applicationContext.getBean(HelloController.class);
```
## 의존 오브젝트 추가
- 스프링 컨테이너는 싱글톤 패턴과 유사하게 애플리케이션이 동작하는 동안 딱 하나의 오브젝트만을 만들고 사용함. 이런 면에서 스프링 컨테이너는 싱글톤 레지스트리라고도 함
- `HelloController`가 기능을 의존해서 사용하는 `SimpleHelloService`라는 클래스를 작성. 이 클래스의 오브젝트가 필요한데 일단 이 작업은 HelloController에서 진행

```java
public String hello(String name) {
    SimpleHelloService helloService = new SimpleHelloService();
    return helloService.sayHello(Objects.requireNonNull(name))
}
```
## Dependency Injection
- 스프링 컨테이너는 DI 컨테이너. 스프링은 DI를 적극적으로 활용해서 만들어져 있고, 스프링을 이용해서 애플리케이션을 개발할 때 DI를 손쉽게 적용할 수 있도록 지원

https://martinfowler.com/articles/injection.html

- Assembler: DI에는 두 개의 오브젝트가 동적으로 의존관계를 가지는 것을 도와주는 제3의 존재
- 스프링 컨테이너는 DI를 가능하게 해주는 어셈블러로 동작

![독립 실행형 스프링 애플리케이션 - 02  Dependency Injection](https://user-images.githubusercontent.com/52024566/229536427-008c8cd7-e4a8-4771-9dd1-17674d4bc0a8.png)

![독립 실행형 스프링 애플리케이션 - 03  Dependency Injection](https://user-images.githubusercontent.com/52024566/229536433-a190314b-9508-4044-b243-a591d8afcf43.png)

- 이제 `SimpleHelloService`도 빈으로 등록을 하고 구현한 인터페이스 타입의 의존 오브젝트로 `HelloController`에 주입해서 사용되도록 함. 주입 방식은 컨트롤러의 파라미터를 이용할 수 있음
## 의존 오브젝트 DI 적용
- 의존 오브젝트를 생성자를 통해서 DI 어셈블러인 컨테이너가 주입을 할 수 있게 생성자 파라미터를 정의
- 주입 받은 오브젝트는 내부 멤버 필드로 저장해두고 이용할 수 있음

```java
public class HelloController {
    private final HelloService helloService;
  
    public HelloController(HelloService helloService) {
        this.helloService = helloService;
    }
  
    public String hello(String name) {
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```

- 주입 받은 의존 오브젝트는 외부에 노출할 필요가 없으니 `private`으로, 이후 변경될 이유도 없으니 `final`로 정의
- 스프링 컨테이너는 빈 오브젝트 사이의 의존관계를 여러 방법을 통해서 자동으로 파악. 만약 빈 클래스가 단일 생성자를 가지고 있다면 생성자의 파라미터 타입의 빈 오브젝트가 있는지 확인하고, 있다면 이를 생성자 호출시 주입
- 명시적으로 의존 오브젝트를 주입하는 정보를 컨테이너에게 제공하려면 `@Autowired`와 같은 애노테이션을 지정할 수 있음
## DispatcherServlet으로 전환
https://docs.spring.io/spring-framework/docs/5.3.x/javadoc-api/org/springframework/web/servlet/DispatcherServlet.html

- 스프링에는 앞에서 만들었던 프론트 컨트롤러와 같은 역할을 담당하는 `DispatcherServlet`이 있음
- `DispatcherServlet`은 서블릿으로 등록되어서 동작하면서 스프링 컨테이너를 이용해서 요청을 전달할 핸들러인 컨트롤러 오브젝트를 가져와 사용
- `DispatcherServlet`이 사용하는 스프링 컨테이너는 `GenericWebApplicationContext`를 이용해서 작성

```java
GenericWebApplicationContext applicationContext = new GenericWebApplicationContext();
applicationContext.registerBean(HelloController.class);
applicationContext.registerBean(SimpleHelloService.class);
applicationContext.refresh();
```
## 애노테이션 매핑 정보 사용
- `DispatcherServlet`은 스프링 컨테이너에 등록된 빈 클래스에 있는 매핑 애노테이션 정보를 참고해서 웹 요청을 전달할 오브젝트와 메소드를 선정할 수 있음

```java
@RequestMapping("/hello")
public class HelloController {
    ...
    @GetMapping
    @ResponseBody
    public String hello(String name) {
        return helloService.sayHello(Objects.requireNonNull(name));
    }
}
```

- 클래스 레벨의 `@RequestMapping`과 메소드 레벨의 `@GetMapping` 두 가지의 정보를 조합해서 매핑에 사용할 최종 정보를 생성
- 컨트롤러 메소드의 리턴값을 웹 요청의 바디에 적용하도록 `@ResponseBody`를 넣어줘야 함. 그렇지 않으면 `String` 타입의 응답은 뷰 이름으로 해석하고 `Thymeleaf`와 같은 뷰 템플릿을 찾으려고 하고 404 에러가 나올 수 있음
- 처음 사용한 `@RestController`는 `@ResponseBody`를 포함하고 있기 때문에 메소드 레벨의 `@ResponseBody`를 넣지 않아도 적용된 것처럼 동작
## 스프링 컨테이너로 통합
- 스프링 컨테이너의 초기화 작업 중에 호출되는 훅 메소드에 서블릿 컨테이너(톰캣)을 초기화하고 띄우는 코드를 삽입

```java
GenericWebApplicationContext applicationContext = new GenericWebApplicationContext() {
    @Override
    protected void onRefresh() {
        super.onRefresh();
        ServletWebServerFactory serverFactory = new TomcatServletWebServerFactory();
        WebServer webServer = serverFactory.getWebServer(servletContext -> {
            servletContext.addServlet("dispatcherServlet",
                    new DispatcherServlet(this))
                .addMapping("/*");
        });
        webServer.start();
    }
};
```

- `DispatcherServlet`를 생성할 때는 현재 실행중인 메소드가 속한 컨테이너 자체를 전달하면 되기 때문에 `this`를 전달할 수 있음
## 자바 코드 구성 정보 사용
- `@Bean` 팩토리 메소드를 사용하면 자바 코드를 이용해서 구성 정보를 만들 수 있음
- 자바 코드를 이용하기 때문에 빈 오브젝트를 직접 생성하고 초기화하는 등의 작업을 명시적으로 작성할 수 있음

```java
@Configuration
public class HellobootApplication {
    @Bean
    public HelloController helloController(HelloService helloService) {
        return new HelloController(helloService);
    }
  
    @Bean
    public HelloService helloService() {
        return new SimpleHelloService();
    }
```

- `@Bean` 메소드의 리턴 타입은 이 빈을 의존 오브젝트로 주입 받을 다른 빈에서 참조하는 타입(주로 인터페이스)으로 지정하는 것이 좋음
- 빈 오브젝트를 생성할 때 주입할 의존 오브젝트는 `@Bean` 메소드의 파라미터로 정의하면 스프링 컨테이너가 이를 전달
- `@Bean` 메소드가 있는 클래스에는 `@Configuration` 애노테이션을 붙여줘야 함
- 자바 코드를 이용한 구성 정보를 사용하려면 `AnnotationConfigWebApplicationContext` 클래스로 컨테이너를 만들어야 함