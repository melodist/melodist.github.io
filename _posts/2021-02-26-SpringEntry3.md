---
layout: post
title: 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 - 3. 회원 관리 예제 - 백엔드 개발
tag: [Spring, SpringEntry]
permalink: /docs/Spring/Entry/3/
date: 2021-02-26 15:08:00
---
# 회원 관리 예제 - 백엔드 개발

## 비즈니스 요구사항 정리

- 데이터: 회원 ID, 이름
- 기능: 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않았다고 가정

![07  비즈니스 요구사항 정리 01](https://user-images.githubusercontent.com/52024566/109257417-71a56380-783b-11eb-8caf-60f1a916734c.png)

- 컨트롤러: 웹 MVC의 컨트롤러 역할
- 서비스: 핵심 비즈니스 로직 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인: 비즈니스 도메인 객체 ex. 회원, 주문, 쿠폰 등 주로 데이터베이스에 저장, 관리됨

![07  비즈니스 요구사항 정리 02](https://user-images.githubusercontent.com/52024566/109257419-72d69080-783b-11eb-870f-9f17d24e7fe9.png)

- 아직 데이터 저장소가 선정되지 않았으므로 우선 인터페이스로 구현 클래스를 변경할 수 있도록 설계
- 데이터 저장소는 RDB, NoSQL 등 다양한 저장소를 고민 중인 상황으로 가정
- 개발을 진행하기 위해서 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소 사용

## 회원 도메인과 리포지토리 만들기

- `MemberRepository`를 인터페이스로 만들고 `MemoryMemberRepository`가 인터페이스를 상속받도록 한다.

- 예제에서는 `long`과 `HashMap`을 사용하였지만 실무에서는 동시성 문제를 고려하여 `ConcurrentHashMap`, `AtomicLong` 사용을 고려한다.

-  `Optional`: NULL이 될 수도 있는 객체를 감싸는 래퍼 클래스의 일종으로 NPE 검사에 드는 비용을 줄인다.

> 참고: [Java Optional 바르게 쓰기](http://homoefficio.github.io/2019/10/03/Java-Optional-바르게-쓰기/)

## 회원 리포지토리 테스트 케이스 작성

개발한 기능을 테스트할 때 자바의 main 메서드나 웹 애플리케이션의 컨트롤러를 통하여 해당 기능을 실행할 경우 다음과 같은 단점이 있다.

1. 준비하고 실행하는데 오래 걸림
2. 반복 실행하기 어려움
3. 여러 테스트를 한 번에 실행하기 어려움

자바는 JUnit이라는 프레임워크로 테스트를 실행해서 이러한 문제를 해결한다.

- `@AfterEach`: 한 번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다. 이렇게 되면 이전 테스트 때문에 다음 테스트가 실패할 수 있다. `@AfterEach`를 사용하면 각 테스트가 종료될 때마다 지정된 기능을 실행한다.

- 테스트는 각각 독립적으로 실행되어야 한다. 테스트 순서에 의존관계가 있으면 안된다.

  ```java
  assertThat(A).isEqualTo(B)
  ```

A와 B가 같지 않을 경우 오류 발생

## 회원 서비스 개발

- `Optional.ifPresent(Consumer<? super T> consumer)`:  Optional 객체가 감싸고 있는 값이 존재할 경우에 실행될 로직을 함수형 인자로 넘긴다.

```java
memberRepository.findByName(member.getName())
	.ifPresent(m -> {
		throw new IllegalStateException("이미 존재하는 회원입니다.");
	});
```

위의 예제에서는 m을 사용하지 않았지만 m은 `findByName()`에서 반환된 객체이다.

- Lambda Expression

```java
s -> System.out.println("Hello, " + s);
```

`s`: Method Signature, 매개변수가 없을 경우 `()`으로 표현

`->`: Lambda Operator

`System.out.println("Hello, " + s)`: Method Implementation. 하나의 문장일 경우 `{}` 생략 가능

## 회원 서비스 테스트

![10  회원 서비스 테스트 02](https://user-images.githubusercontent.com/52024566/109261903-f6947b00-7843-11eb-9cd7-abca9e7ec587.png)

다음과 같이 코드를 수정하여 DI가 가능하다.

- 테스트 코드는 빌드되지 않기 때문에 메소드명은 한글로 적어도 무방하다.
- 테스트를 구축할 때 given-when-then 패턴을 이용하는 것이 좋다.
  - given: 테스트를 위해 준비를 하는 과정으로 객체, 변수등을 정의하는 부분이다.
  - when: 실제로 테스트가 실행되는 부분이다.
  - then: 테스트를 검증하는 과정으로 예상한 값과 실행으로 얻어진 값을 비교하여 검증한다.

- `@BeforeEach`: 각 테스트 실행 전에 호출되어 테스트가 서로 영향이 없도록 항상 새로운 객체를 생성하고 의존관계도 새로 맺어준다.