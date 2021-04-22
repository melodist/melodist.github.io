---
layout: post
title: Docker로 MySQL 실습
tags: [DB, Docker, MySQL, UF]
permalink: /docs/DB/MySQLwithDocker
date: 2021-04-22 13:56:00
---
# Docker로 MySQL 실습

### Docker에서 CentOS Container 실행하기

```bash
# Pull CentOS Image
docker pull centos
```

아무런 옵션 없이 실행할 경우 systemctl을 실행할 수 없으므로 다음과 같이 입력한다.

```bash
# Run CentOS Container
docker run -d --privileged --name centos centos /sbin/init
docker exec -it centos /bin/bash
```

![Docker](https://user-images.githubusercontent.com/52024566/115661916-2e69fb80-a379-11eb-9c5b-6d58b1460f52.png)

### CentOS에 MySQL 설치

```bash
# Install wget
yum -y install wget

# Yum Repository Download
wget --no-check-certificate https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
rpm -Uvh mysql80-community-release-el7-3.noarch.rpm

# Yum Repository Enable
yum repolist all | grep mysql | grep enabled

# Install MySQL Community Server
yum -y install mysql-community-server

# Start MySQL Server
systemctl start mysqld

# Find temporary password
cat /var/log/mysqld.log | grep -i 'temporary password'
```

![02  ServerTest1](https://user-images.githubusercontent.com/52024566/115661909-2d38ce80-a379-11eb-9d7e-fe1b745bf094.png)

임시 패스워드로는 아무런 동작도 할 수 없으므로 비밀번호를 재설정해야 한다.

![02  ServerTest2](https://user-images.githubusercontent.com/52024566/115661912-2dd16500-a379-11eb-97c4-252e20f6346b.png)

비밀번호는 대소문자, 숫자, 특수문자를 모두 포함해야 한다.

![02  ServerTest3](https://user-images.githubusercontent.com/52024566/115661914-2dd16500-a379-11eb-827b-be8692dc00f2.png)

## 일지

- Centos Docker latest Image 사용시 wget이나 rpm으로 https 이용할 수 없는 문제
  --no-check-certificate 옵션으로 해결했지만 완벽한 해결책은 아님
  ca-certificates는 기본적으로 설치되어 있는 상황
- Error: Unable to find a match: mysql-community-server

```bash
yum module disable mysql
yum -y install mysql-community-server
```

## 참고

[모두를 위한 SQL/DB 올인원 패키지 Online.](https://fastcampus.co.kr/data_online_sqldb)

[Error: Unable to find a match: mysql-community-server when installing mysql5.7 in centos7.8 - Programmer Sought](https://www.programmersought.com/article/27293498144/)

[정해영의 블로그 - JEONG Haeyoung's blog: 4월 2019 (genoglobe.com)](http://blog.genoglobe.com/2019/04/)