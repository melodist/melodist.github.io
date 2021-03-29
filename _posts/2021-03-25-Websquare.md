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

## 조회 화면 개발 과정

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
         - 
      3. FulfillmentMapper.getSTD(Dto.getRequest()) 반환

   실제 Dto는 Interface로만 구현되어 있고 실제 정보는 `src/main/resources/mybatis/mapper/fulfillment.xml`에 담겨있음

   `@RequestBody` Annotation은 HTTP POST 요청의 Body를 Java Object로 변환함. 즉, Dto에 Body의 내용이 Java Object 형태로 입력됨.

### Mybatis Mapper 구성

- `<mapper namespace="cj.bts.fw.tpl.fulfillment.dbmapper.FulfillmentMapper">`

이 구문을 통하여 `fulfillment.xml`과 `FulfillmentMapper.java`를 연결

### Lombok

`@Data` Annotaion을 지원하여 Setter, Getter, ToString, Constructor에 대한 정보를 자동으로 생성

`FulfillmentDTO`에는 필드에 관한 정의만 들어있지만 Lombok을 통하여 FulfilmentService에서 `FulfilmentDto.getPageNum()` 등을 호출할 수 있음

### PageHelper

github에서 지원하는 페이징 프레임워크

### Generic

`ArrayList<String>`처럼 해당 ArrayList가 사용할 객체의 타입을 지정해 줌. 다룰 객체의 타입을 미리 명시하여 객체의 형변환을 사용할 필요가 없게 하고 사용하고 싶은 데이터 타입만 사용할 수 있음.

### @RequestBody

@RequestBody는 클라이언트가 전송하는 Http 요청의 Body내용을 Java Object로 변환시켜주는 역할을 한다. 그렇기 때문에 Body가 존재하지 않는 Get 방식의 메소드에 @RequestBody를 활용하는 것은 적합하지 않으므로, 에러가 발생하게 된다. 즉, @RequestBody는 반드시 Post 요청과 함께 사용되어야 한다. @RequestBody는 Json이나 XML과 같은 형태의 데이터를 Jackson 등의 MessageConverter를 활용하여 Java Object로 변환한다. 이러한 성질은 Parameter로 받은 데이터들을 자바 객체로 1대1로 매킹시켜주는 @ModelAttribute와 차이가 있다. 즉, @RequestBody는 POST방식으로 Json의 형태로 넘겨온 데이터를 객체로 바인딩하기 위해 사용할 수 있다.

## 참고

http://docs.inswave.com:1975/

[[SpringBoot\] thymeleaf + Mybatis + PageHelper 페이징 간단예제 (tistory.com)](https://cjw-awdsd.tistory.com/36)

[[JAVA\] Java 제네릭(Generics)이란? (tistory.com)](https://gangnam-americano.tistory.com/47)

[[Spring\] @RequestBody, @ModelAttribute, @RequestParam의 차이 - MangKyu's Diary (tistory.com)](https://mangkyu.tistory.com/72)