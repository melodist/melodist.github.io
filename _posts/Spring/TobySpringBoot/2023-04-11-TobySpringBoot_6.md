---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 06. 자동 구성 기반 애플리케이션
tags: [Spring, TobySpringBoot]
permalink: /docs/Spring/TobySpringBoot_6
date: 2022-04-11 23:07:00
---
# 자동 구성 기반 애플리케이션
- `@AutoConfiguration`의 구조와 동작 원리
- Spring이 제공하는 기능을 SpringBoot가 편리하게 사용할 수 있도록 해줌

## 메타 애노테이션
- 메타 애노테이션
- 애노테이션에 적용된 애노테이션
- 기능 면에서 차이는 없지만 추가적인 정보를 획득할 수 있음
- 애노테이션의 기능을 확장
- 애노테이션을 상속하는 개념은 아님

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component // Meta Annotation
public @interface Service {
}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@UnitTest
@interface FastUnitTest {
}

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.ANNOTATION_TYPE, ElementType.METHOD})
@interface UnitTest {
}
```
    
- Retention과 Target
  - Retention: 메모리 내에서 애노테이션의 유지 시점을 결정
    - SOURCE: 컴파일 할 때 버림
    - CLASS: 런타임에 버림, 기본값
    - RUNTIME: 런타임 종료 시 버림
  - Target: 애노테이션이 적용될 대상
    - TYPE: 클래스, 인터페이스
    - FIELD: 상수 포함 객체 필드
    - METHOD: 메소드
    - PARAMETER: 파라미터에 선언
## 합성 애노테이션
- 하나 이상의 메타 애노테이션이 적용된 애노테이션
- 모든 메타 애노테이션이 적용된 것과 동일한 효과
  - `@RestController` = `@Component` \+ `@ResponseBody`

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration
@ComponentScan
public @interface MySpringBootApplication {
}
```

```java
@Configuration
public class Config {
    @Bean
    public DispatcherServlet dispatcherServlet() {
        return new DispatcherServlet();
    }

        @Bean
    public ServletWebServerFactory servletWebServerFactory() {
        return new TomcatServletWebServerFactory();
    }
}
```
## 빈 오브젝트의 역할과 구분

![자동 구성 기반 애플리케이션 - 01  빈 오브젝트의 역할과 구분](https://user-images.githubusercontent.com/52024566/231475000-0b467275-aa82-44e2-a5aa-92f9e9bfd2d6.png)

### 애플리케이션 로직 빈
- 애플리케이션의 비즈니스 로직을 담고 있는 클래스로 만들어지는 빈
- 컴포넌트 스캐너에 의해서 빈 구성 정보가 생성되고 빈 오브젝트로 등록됨
### 애플리케이션 인프라스트럭처 빈
- 빈 구성 정보에 의해 컨테이너에 등록되는 빈이지만 애플리케이션의 로직이 아니라 애플리케이 션이 동작하는데 꼭 필요한 기술 기반을 제공하는 빈
- 전통적인 스프령 애플리케이션에서는 빈으로 등록되지 않지만 스프링 부트에서 구성 정보에 의 해 빈으로 등록되어지는 `ServletWebServerFactory`나 `DispatcherServlet` 등도 애플리케이션 인프라 빈이라고 볼 수 있음
### 컨테이너 인프라스트럭처 빈
- 스프링 컨테이너의 기능을 확장해서 빈의 등록과 생성, 관계설정, 초기화 등의 작업에 참여하는 빈을 컨테이너 인프라스트럭처 빈, 줄여서 컨테이너 인프라 빈이라고 함
- 개발자가 작성한 구성 정보에 의해서 생성되는 게 아니라 컨테이너가 직접 만들고 사용하는 빈이기 때문에 애플리케이션 빈과 구분
- 필요한 경우 일부 컨테이너 인프라 빈은 주입 받아서 활용할 수 있음
    ![자동 구성 기반 애플리케이션 - 02  빈 오브젝트의 역할과 구분](https://user-images.githubusercontent.com/52024566/231475009-9a0206e7-142e-4fc4-ac6f-b70ba0842d0d.png)

## 인프라 빈 구성 정보의 분리
- `@Import`를 이용하여 스캔 대상이 아닌 클래스를 빈으로 등록할 수 있음
- `DispatcherServlet`이나 `TomcatWebServer` 같은 빈은 컴포넌트 스캔의 대상으로 두지 않음
  - 개발자가 개발한 빈과 분리

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration
@ComponentScan
@Import({DispatcherServletConfig.class, TomcatWebServerConfig.class})
public @interface MySpringBootApplication {
}
```

![자동 구성 기반 애플리케이션 - 04  인프라 빈 구성 정보의 분리](https://user-images.githubusercontent.com/52024566/231771656-f3bf5392-7d01-45d4-aac0-f2733e479005.png)
   
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Import({DispatcherServletConfig.class, TomcatWebServerConfig.class})
public @interface EnableMyAutoConfiguration {
}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration
@ComponentScan
@EnableMyAutoConfiguration
public @interface MySpringBootApplication {
}
```

![자동 구성 기반 애플리케이션 - 03  자동 구성 애노테이션 적용](https://user-images.githubusercontent.com/52024566/231772051-e69d7f7a-2172-4a49-8430-0b4d18791acb.png)

## 동적인 자동 구성 정보 등록
- 위에서는 구성 정보를 하드 코딩했는데 `@EnableMyAutoConfiguration`을 고치지 않고 구성 정보를 변경할 수 있는 방법이 있을까?
- `ImportSelector` 를 구현한 클래스를 사용
  - `ImportSelector`의 구현 클래스를 `@Import`하면 selectImports가 리턴하는 클래스 이름으로 `@Configuration` 클래스를 찾아서 구성 정보로 사용
  - 코드에 의해서 `@import` 대상을 외부에서 가져오고 선택할 수 있는 동적인 방법을 제공
  - 가져올 클래스 정보는 문자열 배열로 리턴

```java
public class MyAutoConfigImportSelector implements DeferredImportSelector {
        @Override
        public String[] selectImports(AnnotationMetadata importingClassMetadata) {
                return new String[] {
                "tobyspring.config.autoconfig.DispatcherServletConfig",
                "tobyspring.config.autoconfig.TomcatWebServerConfig"
            };
        }
}
```

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Import(MyAutoConfigImportSelector.class)
public @interface EnableMyAutoConfiguration {
}
```
## 자동 구성 정보 파일 분리
```java
@Override
public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        List<String> autoConfigs = new ArrayList<>();

        ImportCandidates.load(MyAutoConfiguration.class, classLoader).forEach(autoConfigs::add);

        return autoConfigs.toArray(new String[0]);
}
```

```
tobyspring.config.autoconfig.DispatcherServletConfig
tobyspring.config.autoconfig.TomcatWebServerConfig
```

- `@MyAutoConfiguration` 애노테이션을 만들고 이 클래스 이름 뒤에 `.imports`가 붙은 파일을 `META-INF/spring` 폴더 아래 만들어 `selectImports()`에서 가져와 컨테이너에 등록시킬 `@Configuration` 클래스 목록을 저장
## 자동 구성 애노테이션 적용
- `@Configuration`을 메타 애노테이션으로 가지는 `@MyAutoConfiguration` 애노테이션을 정의 하고 인프라 빈 클래스에 `@Configuration`을 대체해서 부여

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Configuration(proxyBeanMethods = false)
public @interface MyAutoConfiguration {
}
```

```java
@MyAutoConfiguration
public class DispatcherServletConfig {
        @Bean
        public DispatcherServlet dispatcherServlet() {
                return new DispatcherServlet();
        }
}
```

![자동 구성 기반 애플리케이션 - 05  인프라 빈 구성 정보의 분리](https://user-images.githubusercontent.com/52024566/231771666-0618d61d-48bf-4049-95dd-e33f149523f2.png)

## @Configuration과 proxyBeanMethods
- 기반 코드

```java
static class Bean1 {
    private final Common common;

    public Bean1(Common common) {
        this.common = common;
    }
}

static class Bean2 {
    private final Common common;

    public Bean2(Common common) {
        this.common = common;
    }
}

static class Common {
}
```

- 아래의 테스트는 실패

```java
@Test
void configuration() {
    Bean1 bean1 = new Bean1(common())
    Bean2 bean2 = new Bean2(common())

    Assertions.assertThat(bean1.common).isSameAs(bean2.common);
}
```

- 그러나 아래처럼 스프링 컨테이너에서 실행할 경우 테스트는 성공

```java
public class ConfigurationTest {

    @Test
    void configuration() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext();
        ac.register(MyConfig.class);
        ac.refresh();

        Bean1 bean1 = ac.getBean(Bean1.class);
        Bean2 bean2 = ac.getBean(Bean2.class);

        Assertions.assertThat(bean1.common).isSameAs(bean2.common);
    }

    @Configuration
    static class MyConfig {
        @Bean
        Common common() {
            return new Common();
        }

        @Bean
        Bean1 bean1() {
            return new Bean1(common());
        }

        @Bean
        Bean2 bean2() {
            return new Bean2(common());
        }
    }
}
```

- `@MyAutoConfiguration` 애노테이션은 `@Configuration`을 메타 애노테이션으로 가지면서 proxyBeanMethods 엘리먼트를 false로 지정
- proxyBeanMethods는 디폴트로 true 값을 가짐. 이 경우 `@Configuration`이 붙은 클래스는 CGLib을 이용해서 프록시 클래스로 확장을 해서 `@Bean`이 붙은 메소드의 동작 방식을 변경함. `@Bean` 메소드를 직접 호출해서 다른 빈의 의존 관계를 설정할 때 여러번 호출되더라도 싱 글톤 빈처럼 참조할 수 있도록 매번 같은 오브젝트를 리턴하게 함
- 만약 `@Bean` 메소드 직접 호출로 빈 의존관계 주입을 하지 않는다면 굳이 복잡한 프록시 생성 을 할 필요가 없음. 이 경우 proxyBeanMethods를 false로 지정해도 됨. `@Bean` 메소드는 평범한 팩토리 메소드처럼 동작
- proxyBeanMethods는 스프링 5.2 버전부터 지원되기 시작했고 지금은 스프링과 스프링 부트 의 상당히 많은 `@Configuration` 클래스 설정에 적용됨
- `@Bean`은 `@Configuration`이 빈 클래스에서도 사용될 수 있음. 이를 `@Bean` 라이트 모드(lite mode)라고 부름. 빈으로 등록되는 단순 팩토리 메소드로 사용