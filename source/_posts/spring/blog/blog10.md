---
title: 10.[블로그 프로젝트] 부트스트랩으로 메인화면 만들기
date: 2021-09-07 16:56:17
tags:
category:
    - Spring
    - Project
---
웹 UI를 구현할 때, 굳이 html, css를 모두 구현할 필요가 없다.
**Bootstrap**이나 **Materialize**를 통해서 쉽게 구현할 수 있다.


우리는 w3school에서 bootstrap4의 collapsing the navigation bar의 코드를 복사한다.
![](/img/boot/blog/blog10-1.png)


![](/img/boot/blog/blog10-2.png)


이렇게 프로젝트 구조를 만들어서 index.jsp에 우리가 아까 복사한 내용을 덮어쓰운다.
(3번째 줄부터 붙여써야한다.)


그리고 com.cos.blog 하에 controller 패키지를 만든다.
그 패키지 안에 BoardController, UserController 클래스를 만든다.
다음과 같이 작성해보자.
```java
@Controller
public class BoardController {

    @GetMapping({"","/"}) //아무것도 적지 않거나, /를 붙였을 때 모두
    public String index(){
        return "index";
    }
}
```
이렇게 작성 후 localhost:8000/blog/에 접속하면 우리가 아까 만든 index.jsp가 웹에 전달된다..!


그 비밀은 application.yml에 있다.
```yml
spring:
  mvc:
    view:
      prefix: /WEB-INF/views/
      suffix: .jsp
```
보다시피 html을 전달해줘야되는 경우, 미리 경로를 지정한 곳에서 파일을 가져가도록 했다.
![성공한_화면](/img/boot/blog/blog10-3.png)


부트스트랩에서 푸터와 카드 코드를 가져와 약간 수정을 해주면 다음과 같다.
```html
<%--
  Created by IntelliJ IDEA.
  User: MUHN2-031
  Date: 2021/09/07
  Time: 8:06 오후
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Bootstrap Example</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.16.0/umd/popper.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>
</head>
<body>

<nav class="navbar navbar-expand-md bg-dark navbar-dark">
    <a class="navbar-brand" href="/blog">Mudura</a>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#collapsibleNavbar">
        <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="collapsibleNavbar">
        <ul class="navbar-nav">
            <li class="nav-item">
                <a class="nav-link" href="/user/login">로그인</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" href="/user/join">회원가입</a>
            </li>
        </ul>
    </div>
</nav>
</br>

<div class="container">
    <div class="card m-2" >
        <div class="card-body">
            <h4 class="card-title">제목</h4>
            <p class="card-text"> 내용 </p>
            <a href="#" class="btn btn-primary">상세보기</a>
        </div>
    </div>
  <div class="card m-2" >
        <div class="card-body">
            <h4 class="card-title">제목</h4>
            <p class="card-text"> 내용 </p>
            <a href="#" class="btn btn-primary">상세보기</a>
        </div>
    </div>
  <div class="card m-2" >
        <div class="card-body">
            <h4 class="card-title">제목</h4>
            <p class="card-text"> 내용 </p>
            <a href="#" class="btn btn-primary">상세보기</a>
        </div>
    </div>

</div>
<%--푸터자리--%>
<div class="jumbotron text-center" style="margin-bottom:0">
    <p>🧑‍💻 created by Yangdongjue5510</p>
    <p>💭 Backend Dev.</p>
    <p>🌱 Powered by Spring Boot</p>
</div>
</body>
</html>
```
