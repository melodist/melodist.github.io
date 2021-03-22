---
layout: post
title: Java Runnable 예제
tags: [Java]
permalink: /docs/Java/RunnableEx1
date: 2021-03-22 16:58:00
---

# Java Runnable 예제

## 기본 예제

```java
/* 
* main -> Wrapper -> Runnable 순으로 호출하여
* Runnable 예제가 가장 늦게 출력되는 예제 
*/
import java.time.LocalTime;

public class RunnableExample1 {

    static class MyRunnable implements Runnable {
        @Override
        public void run() {
            try {
            Thread.sleep(1000);  // try-catch 쓰지 않을 경우 컴파일 에러 발생
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            String result = "Called at " + LocalTime.now();
            System.out.println(result);
        }
    }
    
    public static void Wrapper() {
        MyRunnable runnable = new MyRunnable();
        Thread thread = new Thread(runnable);
        thread.start();
        System.out.println("Wrapper at " + LocalTime.now());
    }

    public static void main(String[] args) {
        Wrapper();
        System.out.println(LocalTime.now());
    }
}
```

```
Wrapper at 07:57:07.112
07:57:07.118
Called at 07:57:08.093
```

## Lambda 이용 예제

```java
import java.time.LocalTime;

public class RunnableExample1 {

    public static void Wrapper() {
        Thread thread = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            String result = "Called at " + LocalTime.now();
            System.out.println(result);
        });
        thread.start();
        System.out.println("Wrapper at " + LocalTime.now());
    }

    public static void main(String[] args) {
        Wrapper();
        System.out.println(LocalTime.now());
    }
}
```

```
Wrapper at 08:05:20.154
08:05:20.159
Called at 08:05:21.137
```

## 참고

[Java - Runnable과 Callable의 차이점 이해하기 (codechacha.com)](https://codechacha.com/ko/java-callable-vs-runnable/)

[자바: Thread 클래스와 Runnable 인터페이스 | Engineering Blog by Dale Seo](https://www.daleseo.com/java-thread-runnable/)

