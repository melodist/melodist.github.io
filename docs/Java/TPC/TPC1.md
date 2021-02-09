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

## 메서드는 변수다(변수 VS 메서드의 관계)

### 메서드의 매개변수 전달기법(parameter passing)

메서드를 호출(call)하기 위해서는 실인수의 개수와 자료형이 가인수의 개수와 자료형과 일치해야 한다.

1. 값 전달 기법(Call by value)

   ```java
   int a = 10; int b = 20;
   int v = sum(a, b);
   ```

   메소드에는 a와 b의 값인 20이 전달된다.

2. 번지 전달 기법(Call by reference)

   ```java
   int arr[] = {10, 20, 30};
   int v = sum(arr);
   ```

   메소드에는 **arr이 가리키는 주소값(번지)**이 전달된다.

## JVM의 메모리 모델(JVM이 사용하는 메모리 영역 - 4가지)

### JVM이 실행클래스를 실행하는 절차

1. 해당클래스를 현재 디렉토리에서 찾는다.

   -> 실패할 경우 ClassNotFoundException 반환

2. 찾았을 경우 클래스 내부에 있는 static 키워드가 있는 메서드를 메모리, method area의 static zone에 로딩한다.

   - main()에 static이 붙어있지 않으면 main()이 메모리에 로딩되지 않아 호출할 수 없다!

3. static zone에서 main() 메소드를 실행한다.(호출, 시작)

   -> main() 메소드가 호출되면 main() 메소드의 호출정보가 stack area에 들어간다. (push)

   -> 프로그램이 시작되는 부분이다. (Program Counter, PC의 위치가 현재 동작되고 있는 메서드이다.)

4. stack area가 비어있으면 프로그램이 종료된 것이다.

### JVM이 사용하는 메모리 영역

1. method area: method의 byte code가 저장되는 영역으로 static zone과 non-static zone으로 나누어진다.
2. stack area: method가 호출되면 method의 호출 정보가 저장되는 영역으로 call stack frame area라고도 한다.
3. heap area: 객체가 생성되는 영역 (new 연산자)
4. literal area: 문자열 (객체) 상수가 저장되는 영역으로 동일한 문자열이 생성될 경우 별도의 메모리가 확보되지 않고 기존에 생성된 문자열의 주소를 참고하여 재사용한다.

### Non-static 메소드

static이 붙어있지 않은 메소드를 이용하기 위해서는 메소드가 포함된 객체를 생성해야 한다.

new를 이용하여 객체를 생성할 경우 heap area에 객체가 생성되고, non-static area에 객체 내의 non-static 메소드의 byte code가 생성된다. 

객체 내의 메소드는 method area의 non-static zone의 byte code가 할당된 영역을 가리키는 포인터이다.

