---
layout: post
title: MSA 보상 트랜잭션 Framework 구현
tags: [MSA, UF]
permalink: /docs/MSA/CompensatingTransaction
date: 2021-03-05 17:35:00
---



# MSA 보상 트랜잭션 Framework 구현

자기 트랜잭션이 실패했는지 어떻게 아는가

남의 트랜잭션이 실패했는지 어떻게 아는가

보상 트랜잭션을 수행할 경우 이미 Commit 해버렸는데 어떻게 Rollback 하는가

시나리오가 아니라 내부 로직을 PPT 형태로 그려볼 것

REST API의 parameter가 상황마다 다르고 URL도 상황마다 다르다

-> 이를 하나의 API로 통합하려면 어떻게 해야 할까?

스프링 프레임워크를 생각해보자

객체에 애노테이션만 달면 자동으로 DI 처리해줌

내가 개발할 프레임워크도 이런 식으로 구현해야...



트랜잭션을 큐에 올림

성공한 트랜잭션은 스택에 올림

실패했을 경우 보상 트랜잭션 스택에 올린 순서대로 진행



비동기 상황에서는?

트랜잭션이 일어날 때 DB 내의 여러 테이블을 사용하는데

비동기 상황일 경우 아직 성공/실패 Event를 받지 않은 테이블을 전부 lock?



성공한 트랜잭션의 정보와 서비스 주소를 보내고 대기

메시지가 들어오면 다음 서비스의 트랜잭션을 시작

트랜잭션이 완료되면 역순으로 메시지를 보냄

트랜잭션에 문제가 생기거나 응답하지 않을 경우

자신의 트랜잭션을 롤백하고 자신이 저장한 서비스 주소로 **롤백 요청**을 보냄

- 이미 커밋이 된 트랜잭션은 롤백이 가능한가? 
  근데 그게 되면 보상 트랜잭션이라는 걸 안 만들겠지
  - 커밋이 된 트랜잭션은 원칙적으로 롤백이 불가능. DBMS가 지원하는 것을 MSA에 적용하기는 어려움.

타 서비스를 호출해야 할 경우 트랜잭션의 가장 마지막에 하는 것이 원칙

하지만 그렇게 하지 못할 경우가 있다. 이런 케이스에서 타 서비스의 트랜잭션이 실패했다면?

-> 내 작업을 타 서비스의 트랜잭션 이전으로 되돌려야 한다

-> 어떻게?

테이블을 통하여 관리하는 서비스를 만들어 보상 트랜잭션은 이 서비스가 전담케 하고 각 기능의 개발자는 기능 개발에만 집중한다.

각 서비스의 코드를 건드리면 안된다 -> 보상 트랜잭션을 고치면 전 서비스를 다시 배포할 수 없으니

보상 트랜잭션은 각 기능 개발자가 개발한다.

나는 보상 트랜잭션을 수행할 수 있게 하는 로직만 개발하면 된다.

1. 트랜잭션이 실패했을 때 서비스에서 다른 서비스로 취소 전문을 보내야 한다.
2. 어떤 서비스로 어떤 내용을 보내야 할까?
3. 다른 서비스는 이 전문을 어떻게 받을까?

이러한 동작을 각 서비스에서 어떻게하면 프레임워크 형태로 사용할 수 있을까?

각 서비스의 트랜잭션 정보가 담긴 파일을 읽는다

모든 요청을 REST API로 해야 하나?

URI 외에 추가적인 parameter? 재시도 정책 정도

## Saga 패턴

트랜잭션을 DBMS가 아닌 Application이 관리

연속적인 트랜잭션 요청 / 실패 시 Rollback 처리

### 1. Choreography-Based Saga

각각의 App이 트랜잭션 실패 시 보상 Event 발행

구현 시 각 서비스가 구독할 서비스를 지정

### 2. Orchestration-Based Saga

트랜잭션 시작 시 Saga 인스턴스 생성

트랜잭션 종료 시 Saga 인스턴스 소멸

## 고전적인 JDBC

1. JDBC connect
2. INSERT
3. COMMIT
4. CLOSE

- XA: 2PC를 통한 분산 트랜잭션 처리를 위해 X-Open에서 명시한 표준
- Global Transaction: 
- 2 Phase-Commit (2PC): begin -> end -> prepare -> commit

### 트랜잭션 (Transaction)

- 데이터베이스의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위
- 한꺼번에 모두 수행되어야 할 일련의 연산

예를 들어 한 계좌에서 다른 계좌로 출금을 할 때 한 계좌에서 인출이 되고 다른 계좌에서 송금이 되는 일련의 작업은 연속적으로 이루어져야 한다. 트랜잭션은 COMMIT 되거나 ROLLBACK 된다.

## MSA에서의 트랜잭션

Spring -> Mybatis

### Mybatis에서의 트랜잭션

세션연결

Mapper 연결 (Mapper.xml의 <mapper namespace="DAO">)

## API 초안 설계

한 서비스의 트랜잭션이 다른 서비스를 호출하는 과정을 생각해보자



REST API를 만들어 쏴줘야 한다

GET / POST / PUT / DELETE

다른 서비스를 호출하기 위한 주소 / 데이터가 있다면 데이터

어떤 트랜잭션을 취소해야 하는지 알려줘야 한다

어떻게? 이벤트 ID를 찍어줄까? 이건 어떻게 동기화하지?

취소가 실패했을 때는 어떻게 하지?

1. 주문 요청 (클라이언트 -> 주문 / POST)
2. 물류 재고 1 차감 (주문 -> 물류 / PUT)
3. 주문 작성 중 에러

Request / Response

트랜잭션 도중 오류가 발생하면 Response로 취소 전문을 보내야 하나?

DELETE 전문을 받은 URL로 보낸다 -> POST로 **보상 트랜잭션 서비스**를 호출한다

COMMIT을 수행한 DB에는 이전 정보가 남아있지 않으므로 이전 정보도 송신해야 한다

정보의 규격이 제각각인데 이를 어떻게 하나의 API로 담을 수 있을까?

| 원래 서비스 | 보상 트랜잭션            |
| ----------- | ------------------------ |
| /api/stocks | /api/compensation_stocks |

| 에러가 난 서비스                                             | 보상 트랜잭션을 수행해야 할 서비스       |
| ------------------------------------------------------------ | ---------------------------------------- |
| - 보상 트랜잭션 요청 전문을 송신해야 함<br />- 보상 트랜잭션이 완료되었음을 수신해야 함 | - 보상 트랜잭션이 성공했음을 송신해야 함 |

## 클래스, 시퀸스 다이어그램

### 클래스 다이어그램

"클래스"라고 하는 객체지향 설계단위를 이용하여 시스템의 정적인 구조(모델)를 표현한 것이다. 클래스 다이어그램은 분석, 설계, 구현 등 다양한 상황에서 그 사용목적에 맞게 입도를 조절하여 기술할 수 있다.

### 클래스

DAO (Database Access Object): DB별로 만들어야 하나?

- DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 객체
- 필요한 Interface를 DAO에게 던지고 DAO는 이 인터페이스를 구현한 객체를 반환

DTO (Data Transfer Object)

- 계층간 데이터 교환을 위한 객체. 데이터베이스 조회 값을 전달

HttpRequest

HttpResponse

### 클래스 다이어그램 설계

OrchestrationSaga

이 객체가 어떻게 보상 트랜잭션 명령을 내리는가?

StatusDAO / StatusDAOImpl / StatusDTO

1. create(StatusDTO)

2. read
3. update

StdDAO / StdDAOImpl

1. read

### 절차

1. 보상 트랜잭션 ID, 보상 트랜잭션 parameters 넣어서 보상 트랜잭션 요청
   OrchestrationSaga 객체 생성, 객체 내의 CompensationTransaction() 호출
2. COMP_STD에서 보상 트랜잭션 URL 조회
   OrchestrationSaga: StdDAO 객체 생성, 객체 내의 readURL() 호출
   StdDAO: StdVO 객체 생성,  COMP_STD 조회하여 객체 내의 setURL() 호출하고 StdVO 반환
3. COMP_STATUS에 이력 저장
   OrchestrationSaga: StatusDAO 객체 생성, 객체 내의 createStatus() 호출
   StatusDAO: createStatus로 COMP_STATUS에 신규 이력 생성
4. 보상 트랜잭션 전문 생성
   String Parsing
   HttpMessageConverter
5. 보상 트랜잭션 요청
   OrchestrationSaga: WebClient 객체 호출하여 POST / GET 수행
   다수의 요청에 대하여 비동기로 구현하려면?
6. 보상 트랜잭션 응답 받음
   OrchestrationSaga: WebClient의 응답 코드에 따라 다음 작업 수행
7. COMP_STATUS에 결과 기록
   OrchestrationSaga: StatusDAO 객체 생성, 객체 내의 updateStatus() 호출
   StatusDAO: createStatus로 COMP_STATUS 갱신

## 참고

[보상 트랜잭션 | yonghee's blog (wordpress.com)](https://goodyhlee.wordpress.com/tag/보상-트랜잭션/)

[18. Saga 패턴을 활용한 트랜잭션 관리 - 1 :: 북극 펭귄 (tistory.com)](https://cla9.tistory.com/22?category=814447)