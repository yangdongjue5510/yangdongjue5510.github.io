---
title: 14.[블로그 프로젝트] 스프링 시큐리티 체험, 로그인 페이지 커스터마이징
date: 2021-09-13 15:35:26
tags:
category:
    - Spring
    - Project
---
#### Spring starter security
일단 스프링 시큐리티를 이용하기 위해 pom.xml에 있는 관련 dependency를 모두 주석해제하자.
그리고 전통적 방식 로그인 메서드를 주석처리하고 다시 사이트에 들어가려면...
![](/img/boot/blog/blog14-1)
이렇게 시큐리티가 로그인 화면을 띄우게 된다.


이렇게 Spring starter security는 진입할 때 로그인 정보를 입력하도록 한다!

여기에
아이디 : user
비밀번호 : 
콘솔창에 Using generated security password: 뒤에 나온 걸 넣으면 된다.


이렇게 로그인하면 자연스럽게 세션에 적용된다.


#### Spring security taglib
일단 [링크](https://www.baeldung.com/spring-security-taglibs)를 참고하자.
header.jsp에서 태그라이브러리를 쓰기 위해 임포트하자.
```html
<%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>
```
그리고 밑에 다음과 같이 적어주자.
```html
<sec:authorize access="isAuthenticated()">//로그인 됐는지 확인
    <sec:authentication property="principal" var="principal"></sec:authentication>
</sec:authorize>
```
로그인 됐는지 확인하고, 로그인됐으면 html의 principal변수에 세션의 principal 속성을 집어넣는거다.


#### 스프링 시큐리티 로그인 페이지 커스터마이징
일단 전통적인 방법의 로그인과는 달리 user.js로 로그인하지 않을 것이다. 폼으로 전달해줄 것이다.
그러기 위해선 loginForm.jsp를 수정해줘야 한다.
```html
<%--
  Created by IntelliJ IDEA.
  User: MUHN2-031
  Date: 2021/09/07
  Time: 9:39 오후
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="sec" uri="http://www.springframework.org/security/tags" %>

<sec:authorize access="isAuthenticated()">//로그인 됐는지 확인
    <sec:authentication property="principal" var="principal"></sec:authentication>
</sec:authorize>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Yangdongjue blog</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.16.0/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
</head>
<body>

<nav class="navbar navbar-expand-md bg-dark navbar-dark">
    <a class="navbar-brand" href="/">Mudura</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#collapsibleNavbar">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="collapsibleNavbar">
        <c:choose>
            <c:when test=""></c:when>
        </c:choose>
        <c:choose>
            <c:when test="${empty principal}">
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" href="/auth/loginForm">로그인</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/auth/joinForm">회원가입</a>
                    </li>
                </ul>
            </c:when>
            <c:otherwise>
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" href="/board/Form">글쓰기</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/user/Form">회원정보</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/logout">회원정보</a>
                    </li>
                </ul>
            </c:otherwise>
        </c:choose>
    </div>
</nav>
<br>
```


그리고 이제 로그인하지 않아도 접근 그낭한 곳은 /auth를 붙인 uri라고 하고 login과 join은 auth를 붙여서 처리하자.


#### config 패키지 - SecurityConfig 클래스
프로젝트 폴더에 config 패키지를 만들고,
SecurityConfig 클래스를 만들자.
```java
@Configuration  //빈 등록(Ioc)
@EnableWebSecurity //시큐리티 필터를 등록. 필터 설정을 메소드에서
@EnableGlobalMethodSecurity(prePostEnabled = true) //특정 주소를 접근하면 권한/인증을 미리 체크하겠다는 의
public class SecurityConfig  extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()//요청이 왔을 때
                .csrf().disable() //csrf 토큰 비활성화(테스트시에만!!!)
                    .antMatchers("/","/auth/**","/js/**","/css/**","/image/**") // /auth로 시작하거나, static한 데이터는
                    .permitAll() //모두 접근 가능
                    .anyRequest() //이 외의 다른 요청은
                    .authenticated() // 인증해야 접근 가능
                .and() // 인증이 필요한 경우
                    .formLogin() // 로그인 할 수 있도록 한다.
                    .loginPage("/auth/loginForm"); //이 로그인 페이지에서
    }
}
```
이제 인증이 필요한 주소 방문시 -> 로그인 페이지
인증 불필요한 주소 방문시 -> 접근 허용
이렇게 작동한다!!!
csrf 토큰은 반드시 테스트에만 비활성화하라!!
[csrf가_뭔지_모른다면...]()