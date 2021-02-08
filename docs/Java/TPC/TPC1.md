---
layout: default
title: 1
parent: Java TPC(생각하고, 표현하고, 코딩하고)
grand_parent: Java
permalink: docs/Java/TPC/1
---



## 자바의 구동방식(JVM)

Source file은 byte code로 1차 컴파일되고 JVM이 byte code를 읽어 구동될 OS 리소스에 맞게 다시 컴파일하여 실행 코드를 만든다.

Java 프로그램은 JVM(Java Virtual Machine)을 통하여 OS에 독립적으로 실행할 수 있다.

## 이것만 알자! 프로그래밍의 3대 요소(변수, 자료형, 할당)

1. 변수 (Variable): 데이터를 저장할 메모리 공간의 이름(symbol)
2. 자료형 (Data Type): 변수의 크기와 변수에 저장될 데이터의 종류를 결정하는 것
   + 기본자료형(PDT, Primitive Data Type): 컴파일러에서 기본적으로 제공해주는 자료형
   + 사용자정의자료형(UDDT, User-Defined Data Type): 객체 자료형(Object Data Type)
      - 필요에 의해서 새롭게 만들어 사용하는 자료형
      - 만드는 도구, 설계하는 도구, 모델링하는 도구가 필요하다.
3. 할당 (Assign): 변수에 값을 저장(대입, 할당)하는 것

변수선언 : 메모리에 변수(기억공간)를 만드는 것, 변수가 선언되면 ST (Symbol Table)에 등록된다.

## 관계를 이해하라(V. D. A)

변수가 메모리에 저장되는 것처럼 객체도 메모리에 저장되어야 하는데 객체가 메모리에 저장되는 과정을 **객체 생성**이라고 한다.

객체를 생성하기 위하여 `class` 라는 모델링 도구를 이용할 수 있다.

객체 변수는 객체의 주소를 가리킨다. 그리고 객체의 성질을 나타내는 부분은 VO(Value Object)라고 한다. 데이터를 전송하는 객체는 DTO(Data Transfer Object)라고 한다.

```java
int a = 10;
```

`int`: Declaration, DataType

`a`: Variable

`=` : Assign

`10`: Object