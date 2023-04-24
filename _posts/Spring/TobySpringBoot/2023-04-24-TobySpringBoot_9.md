---
layout: post
title: 토비의 스프링 부트 - 이해와 원리 09. 스프링 JDBC 자동 구성 개발
tags: [Spring, TobySpringBoot, UF]
permalink: /docs/Spring/TobySpringBoot_9
date: 2022-04-24 23:02:00
---
# 스프링 JDBC 자동 구성 개발
## 자동 구성 클래스와 빈 설계
- 새로운 기술의 자동 구성 클래스를 작성할 때는 자동 구성 클래스에 적용할 조건과 만들어지는 빈 오브젝트의 종류 등을 먼저 설계

![스프링 JDBC 자동 구성 개발 - 01  자동 구성 클래스와 빈 설계](https://user-images.githubusercontent.com/52024566/234020393-276b2ea8-060e-4874-97f3-2a351222eb2f.png)

- 두 개의 `DataSource` 구현 클래스를 조건에 따라 등록
- 두 개의 빈은 `DataSourceProperties`라는 프로퍼티 클래스를 이용
## DataSource 자동 구성 클래스
- `DataSourceConfig`은 `JdbcOperations` 클래스의 존재를 확인해서 등록
- `DataSource` 빈 메소드에서 프로퍼티로 사용할 프로퍼티를 정의
- `@EnableTransactionManagement`는 애노테이션을 활용한 트랜잭션 기능을 가능하게 해주는 구성용 애노테이션

```java
@MyAutoConfiguration
@ConditionalMyOnClass("org.springframework.jdbc.core.JdbcOperations")
@EnableMyConfigurationProperties(MyDataSourceProperties.class)
@EnableTransactionManagement
public class DataSourceConfig {
```

- `SimpleDriverDataSource`는 간단한 테스트에서만 사용해야 함
- 운영 환경에서 사용하면 안됨
- `@ConditionalOnMissingBean`을 이용해서 앞에서 `DataSource`가 등록되면 빈을 생성하지 않음

```java
@Bean
@ConditionalOnMissingBean
DataSource dataSource(MyDataSourceProperties properties) throws ClassNotFoundException {
    SimpleDriverDataSource dataSource = new SimpleDriverDataSource();
  
    dataSource.setDriverClass((Class<? extends Driver>) Class.forName(properties.getDriverClassName()));
    dataSource.setUrl(properties.getUrl());
    dataSource.setUsername(properties.getUsername());
    dataSource.setPassword(properties.getPassword());
  
    return dataSource;
}
```

Hikari Data Source는 Hikari 클래스가 존재하는 경우에만 만들어지도록 조건을 걸어줌

```java
@Bean
@ConditionalMyOnClass("com.zaxxer.hikari.HikariDataSource")
@ConditionalOnMissingBean
DataSource hikariDataSource(MyDataSourceProperties properties) {
    HikariDataSource dataSource = new HikariDataSource();
  
    dataSource.setDriverClassName(properties.getDriverClassName());
    dataSource.setJdbcUrl(properties.getUrl());
    dataSource.setUsername(properties.getUsername());
    dataSource.setPassword(properties.getPassword());
  
    return dataSource;
}
```
