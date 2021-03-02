---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 4. 스프링 빈과 의존관계
tag: [Spring, SpringEntry]
permalink: /docs/Spring/Entry/4
---
# 스프링 빈과 의존관계

## 컴포넌트 스캔과 자동 의존관계 설정

### 스프링 빈을 등록하는 2가지 방법

1. 컴포넌트 스캔과 자동 의존관계 설정
2. 자바 코드로 직접 스프링 빈 등록하기

`@Controller`, `@Service`, `@Repository`를 이용하면 스프링 컨테이너에 빈(bean)을 등록할 수 있다. 
`@Autowired`를 생성자에 이용하면 스프링 컨테이너에서 생성자에 연관된 객체를 가져온다. 이렇게 객체 의존관계를 외부에서 넣어주는 것을 **DI (Dependency Injection), 의존성 주입**이라 한다.

### 컴포넌트 스캔 원리

* `@Component`가 있으면 스프링 빈으로 자동 등록됨.  

![컴포넌트 스캔과 자동 의존관계 설정 01](https://user-images.githubusercontent.com/52024566/105950562-eefd7d00-60b1-11eb-826f-096c31eac8a4.png)

* `@Controller`, `@Service`, `@Repository`도 `@Component`를 포함하고 있으므로 자동 등록된다.

스프링은 스프링 컨테이너에 빈을 등록할 때, 기본으로 Singleton으로 등록한다.  
**따라서, 같은 스프링 빈이면 모두 같은 Instance이다.**  
Singleton이 아니게 설정할 수는 있지만 대부분 Singleton을 사용한다.

## 자바 코드로 직접 스프링 빈 등록하기

![자바 코드로 직접 스프링 빈 등록하기 01](https://user-images.githubusercontent.com/52024566/105950726-36840900-60b2-11eb-9fb0-9d8c05da44d0.png)  

- Dependency Injection (DI)에는 필드 주입, setter 주입, 생성자 주입이 있다.
- 의존관계가 실행 중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장한다.
- 실무에서는 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드에 컴포넌트 스캔을 사용한다. 
- 정형화되지 않거나, 상황에 따라 구현 클래스를 변경해야 하면 설정을 통해 스프링 빈으로 등록한다. 
- 본 강의에서는 **메모리 리포지토리를 다른 리포지토리로 변경**할 예정이기 때문에 자바 코드로 직접 스프링 빈을 등록함. 
- `@Autowired`를 통한 DI는 스프링이 관리하는 객체에서만 동작한다. 사용자가 직접 생성한 객체에서는 동작하지 않는다.