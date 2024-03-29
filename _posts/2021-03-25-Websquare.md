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

## 화면 개발 과정

1. xml 파일 작업 -> component 배치
2. 버튼 component에 event 연결
3. Grid에 대응하는 DataCollection 생성
4. DataCollection과 Server를 연결하는 Submission 생성
5. Mybatis Mapper 생성

## 보상 트랜잭션 기준 정보 조회 화면 개발 과정

1. 화면 그리기
2. DatacCollection 생성
3. Submission 생성 (SubmissionSearch: 조회 화면 담당 / SubmissionResult: 조회 결과 담당)
4. Service 

### 조회 기능 동작 과정

1. 조회 조건 입력 -> DataCollection: dlt_searchParam

2. 조회 버튼 클릭 -> scwin.BtnSerach_onclick 이벤트 발생

3. scwin.BtnSerach_onclick 함수 실행

   1. dma_searchResult.removeAll() 실행 // dma: dataMap

   2. scwin.searchMain(1, "Y") 실행 // searchMain(idx: 조회시작 순번, strYn: 총 건수 조회 여부)

   3. dma_searchParam.set("pageNum", idx);

   4. com.sbm.executePage(paramMap=dma_searchParam, sbmObj=submissionMaster);

   5. com.sbm.execute(sbmObj=submissionSearch, requestData=null, compObj=null)

   6. submissionSearch 정보에 의해 gcm.ACTION_PREFIX.CM/fw/tpl/fulfillment/search에 POST 요청

   7. WAS에서 FulfillmentController에 등록된 정보에 따라 getSTDpage 호출

      1. FulfillmentService의 getSTD(DTO)로 모든 정보 얻어옴
      2. getPageSize() 통하여 page 구분
      3. Json 객체 data 생성하여 결과를 json 객체에 담아서 반환

   8. FulfillmentService에서 getSTD(DTO: CommonPageDto, 공통 활용) 호출

      1. FulfillmentService에서 FulfillmentMapper 생성

      2. PageHelper.startPage(Dto.getPageNum(), Dto.getPageSize())

         :arrow-right: CompStdInquire에서 set("pageNum", idx)를 통하여 입력됨

      3. FulfillmentMapper.getSTD(Dto.getRequest()) 반환

   실제 Dto는 Interface로만 구현되어 있고 실제 정보는 `src/main/resources/mybatis/mapper/fulfillment.xml`에 담겨있음

   `@RequestBody` Annotation은 HTTP POST 요청의 Body를 Java Object로 변환함. 즉, Dto에 Body의 내용이 Java Object 형태로 입력됨.

### 조회 테이블 (TcompStd) 구조

| 컬럼      | 유형          | 컬럼명                    |
| --------- | ------------- | ------------------------- |
| ID        | VARCHAR2(5)   | 보상 트랜잭션 서비스 ID   |
| CATEGORY  | VARCHAR2(2)   | 보상 트랜잭션 서비스 구분 |
| URL       | VARCHAR2(256) | 보상 트랜잭션 서비스 URL  |
| SYNC_FLAG | CHAR(1)       | 동기/비동기               |
| HTTP_FLAG | CHAR(1)       | http/https 여부           |
| REST_FLAG | CHAR(1)       | GET/POST                  |
| RETRY_STD | NUMBER(1)     | 허용된 재시도 횟수        |
| INS_ID    | VARCHAR2(10)  | 입력ID                    |
| INS_DTM   | DATE          | 입력일시                  |
| MOD_ID    | VARCHAR2(10)  | 수정ID                    |
| MOD_DTM   | DATE          | 수정일시                  |

### 저장 기능 동작 과정

1. dlt_transactionStdList 편집

2. 저장 버튼 클릭 -> scwin.BtnSave_onclick 이벤트 발생

3. scwin.BtnSave_onclick 함수 실행

   1. com.sbm.execute(saveSubmission) 실행
   2. saveSubmission에 지정된 URL gcm.ACTION_PREFIX.CM/fw/tpl/fulfillment/save에 POST 요청

4. WAS에서 FulfillmentController에 등록된 정보에 따라 saveStd호출

   1. FulfillmentService에서 saveStd() 호출
   2. 갱신한 열 갯수 resultFlag에 담아서 반환

5. FulfillmentService에서 `saveStd(stdList: List<FulfillmentDto>)` 호출

   1. stdList를 돌면서 반복문 수행

   2. rowStatus를 확인하고 rowStatus에 따른 CUD 수행

      :arrow-right: CompStdInquire에서 set("pageNum", idx)를 통하여 입력됨

   3. FulfillmentMapper.getSTD(Dto.getRequest()) 반환

### 프론트엔드에서 고려해야 할 것

- GET 선택시 동기/비동기 중 동기로 고정. 재시도 횟수는 0으로 선택 불가능하게.
- POST 선택시 동기/비동기, 재시도 횟수 초기화가 더 불편한듯

## 보상 트랜잭션 수행 이력 조회 화면 개발 과정

### 보상 트랜잭션 이력 테이블 (TCOMP_STATUS) 구조

| 컬럼             | 유형         | 컬럼명                    |
| ---------------- | ------------ | ------------------------- |
| COMPTRX_ID       | VARCHAR2(25) | 보상 트랜잭션 ID          |
| SERVICE_ID       | VARCHAR2(5)  | 보상 트랜잭션 서비스 ID   |
| SERVICE_CATEGORY | VARCHAR2(2)  | 보상 트랜잭션 서비스 구분 |
| COMPTRX_CALLER   | CHAR(1)      |                           |
| SYNC_FLAG        | CHAR(1)      | 동기/비동기               |
| HTTP_FLAG        | CHAR(1)      | http/https 여부           |
| REST_FLAG        | CHAR(1)      | GET/POST                  |
| RETRY_STD        | NUMBER(1)    | 허용된 재시도 횟수        |
| INS_ID           | VARCHAR2(10) | 입력ID                    |
| INS_DTM          | DATE         | 입력일시                  |
| MOD_ID           | VARCHAR2(10) | 수정ID                    |
| MOD_DTM          | DATE         | 수정일시                  |

## 보상 트랜잭션 요청 기능 개발 과정

### 보상 트랜잭션 요청 기능 동작 Timeline

### Mybatis Mapper 구성

- `<mapper namespace="cj.bts.fw.tpl.fulfillment.dbmapper.FulfillmentMapper">`

이 구문을 통하여 `fulfillment.xml`과 `FulfillmentMapper.java`를 연결

- `<select id="getStd" resultType="cj.bts.fw.tpl.fulfillment.dto.FulfillmentDto">`

SQL 쿼리를 입력하는 부분. `select` 는 DB의 CRUD를 나타냄 (insert, delete, update, select)

- `ID AS id`: ID(DB 쿼리명)을 id(DataCollection id)로

- `<if test> </if>`: 동적 SQL
- `#{var}`

### Lombok

`@Data` Annotaion을 지원하여 Setter, Getter, ToString, Constructor에 대한 정보를 자동으로 생성

`FulfillmentDTO`에는 필드에 관한 정의만 들어있지만 Lombok을 통하여 FulfilmentService에서 `FulfilmentDto.getPageNum()` 등을 호출할 수 있음

### PageHelper

github에서 지원하는 페이징 프레임워크

### Generic

`ArrayList<String>`처럼 해당 ArrayList가 사용할 객체의 타입을 지정해 줌. 다룰 객체의 타입을 미리 명시하여 객체의 형변환을 사용할 필요가 없게 하고 사용하고 싶은 데이터 타입만 사용할 수 있음.

### @RequestBody

@RequestBody는 클라이언트가 전송하는 Http 요청의 Body내용을 Java Object로 변환시켜주는 역할을 한다. 그렇기 때문에 Body가 존재하지 않는 Get 방식의 메소드에 @RequestBody를 활용하는 것은 적합하지 않으므로, 에러가 발생하게 된다. 즉, @RequestBody는 반드시 Post 요청과 함께 사용되어야 한다. @RequestBody는 Json이나 XML과 같은 형태의 데이터를 Jackson 등의 MessageConverter를 활용하여 Java Object로 변환한다. 이러한 성질은 Parameter로 받은 데이터들을 자바 객체로 1대1로 매킹시켜주는 @ModelAttribute와 차이가 있다. 즉, @RequestBody는 POST방식으로 Json의 형태로 넘겨온 데이터를 객체로 바인딩하기 위해 사용할 수 있다.

### Oracle 텍스트 검색

### H2 데이터베이스
- **schema.sql**: 테이블 생성
- **data.sql**: 데이터 입력

기본 TCP 포트 9092 -> 변경 원할 시 바로가기 경로 -tcpPort xxxx 추가

### Serializable

```java
@Data
public class CommonPageDto<T> implements Serializable {
    private static final long serialVersionUID = 1L;

    private int pageNum;

    private int pageSize;

    private T request;
}
```

직렬화 객체. Stream 생성 원할 시 사용.

## Daily Report

### 20210329

1. 화면에서 넘어온 값 표시 안되는 문제

   -> GridView의 Body 각각에 id를 지정해줘야 함

2. serviceCategory, serviceID, serviceURL, description 안 넘어오는 문제

   ->  내가 보고 있었던 것은 DB로 넘어가기 전의 객체였음. 따라서 정상적으로 넘어갔음

### 20210330

1. 설명 깨지는 문제

   -> H2 DB의 data.sql 파일의 인코딩 문제 (ANSI였음)

2. 보상 트랜잭션에 UID 자동으로 부여하는 방법은?

   -> DB마다 방법은 다르지만 특정 컬럼에 자동으로 증가하는 값 부여 가능

3. 서비스 수행일자로 DB 조회하는 방법은?

   -> 단순히 TIMESTAMP로 비교하면 됨

### 20210331

1. invalid value 1 for parameter parameterindex

   -> SQL문의 따옴표('), 세미콜론(;) 등이 잘못 사용되지 않았는지 점검.

   특히 SQL문을 DB에서 사용할 경우 세미콜론을 사용하는데 xml에서는 사용하면 안됨

2. Mybatis 사용 시 부등호 그냥 쓰면  괄호인지 부등호인지 구분할 수 없기 때문에 그냥 쓰면 안됨.

   -> CDATA로 감싸서 해결

3. InputCalendar가 입력값 못 받는 문제

   -> ref 값으로 datacollection과 매칭함

4. 저장시 서버 오류 뜨는 문제

   -> controller에 대응하는 service가 없을 경우 발생

5. DB에서 얻은 코드를 풀어서 표현

   -> Table join도 가능하나 autoComplete 사용

6. 웹스퀘어 날짜 표현

   -> 웹스퀘어에서 제공하는 API가 그지같아서 javascript 사용

7. 파일 호출한 곳 찾기 : Ctrl+Shift+G

### 20210401

1. DAO는 어디로 갔나?

   -> dbmapper가 DAO 역할을 한다.

2. DAOImpl은 어디로 갔나?

   -> dbmapper가 Interface 형태로 구현되었고 DAOImpl은 .xml이 한다.

3. GridView 입력값 검증은 어떻게?

4. 시연 시나리오

   원래 서비스 -> 요청 서비스 -> 원래 서비스 에러

### 20210407

1. UPDATE시 ListDto 필요. @RequestBody도 필요. dma가 아니라 dlt를 넘김.

2. 결제 성공 / 실패 구분은 어떻게?

   성공 : 결제 성공 INSERT / 결제 조회 콜백

   실패 : 결제 실패 INSERT / 보상 트랜잭션 호출 / 결제 조회 콜백

3. GridView와 연결된 Table을 수정할때 GridView와 연결된 dlt를 ref로 넘겨야됨

4. https 요청을 할 때 추가적으로 필요한 것이 있는가?
   보증서 등을 추가해야 하는데 일단 보류

5. 비동기 요청이 잘 되었는지 어떻게 검증하는가?
   Log로 간단하게 검증이 가능했다. Async 이후의 과정이 먼저 발생하는 것을 확인.

6. 주문 상태를 Update하는 과정을 어떻게 할까?
   결제에서 API로 그냥 update

7. RestTemplate을 WebClient로 바꿀 수 있을까?

8. 서비스에서 타 서비스를 API로 호출하려면 어떻게 해야할까?
   그리고 호출할 때 JSON 객체를 보내려면 어떻게 해야할까?

### 20210408

1. 보상 트랜잭션 기준 정보에서 조회가 되지 않았을 경우 예외 처리 필요

2. TRUNCATE로 주문 테이블 초기화하는 과정에서 주문 번호 초기화되지 않는 문제

   ALTER TABLE ALTER COLUMN ID RESTART WITH 1로 해결

3. Bootstrap 테마 적용 가능한지

   외부 스크립트 적용 - Style 뷰에서 각 컴포넌트 설정에서 원하는 클래스 입력하여 적용

4. 메뉴 적용하여 주문/물류/결제 분리하여 표시할 수 있을지

   Bootstrap 도입하고 iframe으로 표시

5. 원하는 View 다시 불러오는 방법

   Quick Access - 원하는 View 입력

6. 어떤 기능을 Service에 넣고 어떤 기능을 Controller에 넣지?

   보상 트랜잭션 호출, 주문 상태 수정 모두 Transaction 하에 연속적으로 일어나므로 Service에 넣는다.

### 20210409

1. WebSquare 메시지 팝업 폰트 문제

2. CSS 외부 폰트 사용 방법

   css 스타일 시트 @font-face

3. dropdown 안 닫히는 문제

   최상위 Body에 container-fluid p-0 m-0 속성 부여

### 20210412

1. Table 오른쪽 다 못 채우는 문제

   상위 display: flex; 자신 float: right; / 모두 container-fluid

2. 글자 크기도 반응형으로

## 참고

http://docs.inswave.com:1975/

[[SpringBoot\] thymeleaf + Mybatis + PageHelper 페이징 간단예제 (tistory.com)](https://cjw-awdsd.tistory.com/36)

[[JAVA\] Java 제네릭(Generics)이란? (tistory.com)](https://gangnam-americano.tistory.com/47)

[[Spring\] @RequestBody, @ModelAttribute, @RequestParam의 차이 - MangKyu's Diary (tistory.com)](https://mangkyu.tistory.com/72)

[[MyBatis\] 동적 쿼리 if문 문법 총 정리 (tistory.com)](https://java119.tistory.com/42)

[Mybatis 에서 CDATA 사용하기 :: 물고기 개발자의 블로그 (tistory.com)](https://epthffh.tistory.com/entry/Mybatis-에서-CDATA-사용하기)