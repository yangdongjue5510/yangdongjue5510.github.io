---
title: 1. [블로그 프로젝트] 개발 환경, 의존성 설정하기.
date: 2021-08-26 13:09:25
tags:
category:
    - Spring
    - Project
---
이번엔 스프링부트를 통해 블로그를 한번 만들어보자!
유튜버 데어프로그래밍의 강좌를 보며 제작한다!!!


먼저 Oracle JDK SE와 MySQL을 설치해주자.
JDK는 홈페이지에서 다운받고, MySQL은 맥의 homebrew로 설치가 가능하다!
[MySQL 설치 관련 링크](https://whitepaek.tistory.com/16)


그리고 이클립스에서 스프링을 사용할 수 있게해주는 STS 툴 이라는 게 있다.
나는 인텔리제이를 써서 생략하겠다.

그리고 어떤 에디터를 쓰든 utf-8으로 인코딩 설정을 해주자!!!

> utf-8?

자 1bit = 2^1 는 0과 1 즉 두가지 결과를 만들어낸다.
8bit = 2^8 = 256 = 1 byte 이다.


자 이제 영어 알파벳을 생각해보자. 대소문자 다합쳐서 50여개다.
여기에 숫자와 몇몇 특수기호를 합치면 꽤 많이 될 것이다.
만약 256개 이하라면, 우리는 문자 1개당 하나씩 할당해주면 
모든 문자를 이진수로 표현할 수 있다!


근데 한글과 중국한자은 어떻게 한다는 말인가!
한글은 대략 2byte가 필요하고, 한자는 3byte가 필요하다.


우리가 설정한 UTF-8은 3byte를 가진 문자체계다.
즉 영어, 한글, 한자를 모두 소화할 수 있는 인코딩 체계라는 말씀!!

## 의존성 설정하기.
이번 프로젝트에서는 maven으로 패키지를 관리한다.
그리고 의존성에서 우리가 사용할 것들을 살펴보자.
1. Spring boot Dev Tool : 스프링의 성능을 향상 시키고, 개발을 편리하게 돕는 도구 정도로 이해하자.
2. Lombok : getter, setter같은 메소드를 어노테이션으로 퉁치는 기능을 제공해주는 도구
3. JPA : 자바 객체를 변환해서 데이터베이스에 저장할 수 있게 해주는 도구
4. MySQL Driver : MySQL을 사용할 수 있게 해주는 도구
5. Spring Web : 각종 어노테이션과 내장 톰캣을 사용할 수 있게해주는 도구
6. Spring Security : 세션, 권한 등 다양한 보안 기능을 제공하는 도구


사용할법하지만 사용하지 않는 의존성 기능들도 참고 삼아 알아보자.
- OAuth2 Client : 카카오톡 같은 소셜 로그인을 지원하는 도구(하지만 우리는 학습을 위해 직접 구현)
- 각종 템플릿 : 우리는 JSP를 사용할 거다.(스프링은 JSP를 제공하지 않으므로 따로 폴더파서 만들어줘야됨)
    - 좀 더 디테일하게 말하면, 스프링 프로젝트의 source/temple 폴더에 JSP를 만들어도 인식을 못한다.

![대략적인 구조](/img/boot/blog/blog1-1.png)

> 잠깐 주의사항

패키지를 다룰 때 패키지 위치를 정확하게 해줘야된다.
java의 com.cos.blog안에 패키지를 넣어야 알맞은 IoC가 실행된다.

***

그리고 우리는 몇가지 의존성을 더 넣어줄꺼다.
**pom.xml**이라는 파일이 있다. 여기에 각종 dependecies가 있는데 우리는 다음을 복붙해줄거다
```xml
<!-- 시큐리티 태그 라이브러리 -->
<dependency>
  <groupId>org.springframework.security</groupId>
  <artifactId>spring-security-taglibs</artifactId>
</dependency>

<!-- JSP 템플릿 엔진 -->
<dependency>
  <groupId>org.apache.tomcat.embed</groupId>
  <artifactId>tomcat-embed-jasper</artifactId>
</dependency>

<!-- JSTL -->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>jstl</artifactId>
</dependency>
```
그리고 우리는 MySQL, JPA, Security를 사용하지 않으므로, 주석처리해주자.