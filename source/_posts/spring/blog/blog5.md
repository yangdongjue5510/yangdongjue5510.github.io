---
title: 5. [블로그 프로젝트] Maven, Lombok, application.yml
date: 2021-08-26 20:11:05
tags:
category:
    - Spring
    - Project
---
## Maven
자 만약에 우리가 프로젝트를 할 때 어떤 라이브러리를 사용한다고 하자.
우리에게는 3가지 시나리오가 있다.


1. 라이브러리를 다운받고, 각 프로젝트의 lib 폴더에 복사하기.
    - 프로젝트 만들 때마다 복사해야 된다...
2. 라이브러리를 외부에 두고 프로젝트들이 가져다 쓰기
    - 하지만 배포할 때 라이브러리가 포함되지 않아서 힘들다..
3. 중앙 저장소에 라이브러리들을 저장하고, 프로젝트에 어떤 라이브러리를 쓸지 써놓기
    - pom.xml에 어떤 라이브러리를 쓸 지 써놓는다!
    - 빌드 과정에서 pom.xml보고 라이브러리 다운받는다!


## Lombok
(다운을 받아야하는데 이건 검색을 통해 해결해자!)
일단 간단하게 알아보자
- @Data : getter와 setter를 만듬
- @AllArgsConstructor : 모든 클래스멤버를 인자로 받고 초기화하는 생성자 만듬
- @RequiredArgsConstructor : 클래스 멤버 중 final 키워드가 붙은 멤버들만 인자로 받는 생성자 만듬
- @NoArgsConstructor : 빈 생성자 만듬


## yaml (yml)
스프링 프로젝트의 전반적인 설정을 해놓는 파일.
원래는 xml에 저장했으나 최근에는 yml을 주로 사용
yaml은 json, xml보다 가벼움.


키와 밸류 사이에는 스페이스가 한칸 띄어져 있어야 한다!(중요!)
json의 중괄호 대신, yml은 들여쓰기(스페이스 두번)으로 대체한다!


## application.yml
스프링의 모든 설정을 다루는 파일.
스프링 부트를 시작하기 전에 먼저 application.yml을 읽는다!
web.xml, root-context.xml, servlet-context.xml의  합본!


우리 프로젝트의 application.yml에 다음을 복붙하자
```yml
server:
  port: 8000
  servlet:
    context-path: /blog
    encoding:
      charset: UTF-8
      enabled: true
      force: true
    
spring:
  mvc:
    view:
      prefix: /WEB-INF/views/
      suffix: .jsp
      
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/blog?serverTimezone=Asia/Seoul
    username: 데베계정이름
    password: 데베 비번
    
  jpa:
    open-in-view: true
    hibernate:
      ddl-auto: create
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
      use-new-id-generator-mappings: false
    show-sql: true
    properties:
      hibernate.format_sql: true

  jackson:
    serialization:
      fail-on-empty-beans: false
```


1. server : 내 서버의 진입점을 정의. 비워놓을시 8080포트에 /로 초기화
2. mvc : jsp 연결을 위해 설정
  - @Controller는 파일을 리턴하는데, 기본경로가 "src/main/resources/static"이다.
  - 이때 그냥 "file.html"을 리턴하면, "src/main/resources/staticfile.html"을 찾게된다.
  - 그래서 "/file.html"을 리턴해줘야 올바른 경로를 찾는다.
  - static 폴더는 브라우저가 인식가능한, css, html, js, png 등등.. 만 담는다.
  - 컴파일이 필요한 동적파일(jsp 등)은 static에 넣으면 인식못한다.
  - 그래서 mvc는 @Controller의 return이 어느 위치에서 찾을지를 설정한다.
  (위 예시에서는 views폴더에서 리턴값에 .jsp를 붙인 파일을 찾는다.)
  (이렇게 하면 톰캣이 jsp를 컴파일해서 브라우저에 전달한다.)