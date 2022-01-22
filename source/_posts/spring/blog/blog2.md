---
title: 2. [블로그 프로젝트] HTTP 1.1과 MIME type, MySQL프로젝트 연결하기
date: 2021-08-26 14:13:05
tags:
category:
    - Spring
    - Project
---
## HTTP
[HTTP_더_알아보기](https://yangdongjue5510.github.io/2021/08/25/Spring/boot/boot3/)
HTTP는 문서를 전송하는 stateless 통신 프로토콜이다!
종류는 다음과 같다!
**GET** : 데이터 줘!
**POST** : 데이터 추가해줘!
**PUT** : 데이터 수정해줘!
**DELETE** : 데이터 삭제해줘!


그렇다면 '어떤' 데이터를 요청하거나 보낼지를 알려줄까?
이는 쿼리 파라미터(URI의 ? 부분)를 쓰거나,
MIME type으로 알려줄 수 있다!

## MIME type
HTTP는 데이터를 패킷이라는 단위로 나눠서 보낸다.
이 패킷은 헤더+바디 인데, 헤더는 보내는 데이터에 대한 설명이고, 바디는 데이터를 담는다.


이때 헤더에는 우리가 보낼 데이터가 어떤 데이터인지를 알려주는 **MIME type**을 포함한다.
MIME type은 type/subtype형태를 가진다.
[MIME_type_더알아보기](https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)

## MySQL 연결하기

MySQL를 제대로 설치했다면
mysql -uroot -p 로 접근이 가능하고,
mysql.server start로 데이터베이스를 시작할 수 있다.


이렇게 설치된 상황에서
**mysql -uroot -p**로 mysql을 열고,
```mysql
-- 유저이름@아이피주소
create user 'cos'@'%' identified by 'cos1234';
-- ON DB이름.테이블명
-- TO 유저이름@아이피주소
GRANT ALL PRIVILEGES ON *.* TO 'cos'@'%';
CREATE DATABASE blog CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
use blog;
```
를 입력하면 우리가 활용할 db가 생성된다.
그리고 디비 서버를 켜주자.


그런 다음 우리 프로젝트 폴더 중 resources 폴더 안에는
**application.properties**라는 파일이 있다.
이 파일의 확장자를 **yml**로 바꾼 다음에 이렇게 써주자.


```json
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/blog?serverTimezone=Asia/Seoul
    username: 디비 계정
    password: 디비 비밀번호
```
(yml은 json 형식이다. 그래서 properties보다 덜 써도 같은 효과를 본다.)
(url은 시간 설정이라고 생각하자.)
이렇게 하면 db 연결은 끝이다!!!