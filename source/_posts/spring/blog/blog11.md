---
title: 11.[블로그 프로젝트] 로그인, 회원가입 화면 만들기
date: 2021-09-07 21:21:33
tags:
category:
    - Spring
    - Project
---
#### 템플릿 구성하기
우리 블로그에서 모든 페이지에서 푸터가 동일하게 적용될 수 있도록 템플릿을 구성해보자.
스크립트 태그를 바디가 끝나는 태그 바로 위에 두자.(다른방식도 있으니 유의하자.)
views 폴더 안에 layout 폴더를 만들고, footer.jsp, header.jsp를 만들자.


그리고 index.jsp에서 헤더에 해당하는 부분을 header.jsp로 옮기고,
푸터도 index.jsp에서 푸터에 해당하는 부분을 footer.jsp로 가져온다.


그 대신 index.jsp에서 원래 헤더가 있어야 할 자리에
```html
<%@ include file="layout/header.jsp"%>
```
푸터가 있어야할 자리에는
```html
<%@ include file="layout/footer.jsp"%>
```
이 코드를 넣어주면 정상작동한다!


#### 회원가입, 로그인 페이지 컨트롤러 메소드 만들기
```java
@Controller
public class UserController {
    @GetMapping("/user/joinForm")
    public String joinForm(){
        return "user/joinForm";
    }
        @GetMapping("/user/loginForm")
    public String loginForm(){
        return "user/loginForm";
    }
}
```


#### joinForm.jsp, loginForm 만들기
부트스트랩4 staked form을 활용.
**joinForm.jsp**
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">
    <form action="/action_page.php">
        <div class="form-group">
            <label for="username">Username</label>
            <input type="text" class="form-control" placeholder="Enter username" id="username">
        </div>
        <div class="form-group">
            <label for="email">Email address:</label>
            <input type="email" class="form-control" placeholder="Enter email" id="email">
        </div>
        <div class="form-group">
            <label for="password">Password:</label>
            <input type="password" class="form-control" placeholder="Enter password" id="password">
        </div>
        <div class="form-group form-check">
            <label class="form-check-label">
                <input class="form-check-input" type="checkbox"> Remember me
            </label>
        </div>
        <button type="submit" class="btn btn-primary">회원가입</button>
    </form>

</div>

<%@ include file="../layout/footer.jsp"%>
```
loginForm.jsp
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">
  <form action="/action_page.php">
    <div class="form-group">
      <label for="username">Username</label>
      <input type="text" class="form-control" placeholder="Enter username" id="username">
    </div>
    <div class="form-group">
      <label for="password">Password:</label>
      <input type="password" class="form-control" placeholder="Enter password" id="password">
    </div>
    <div class="form-group form-check">
      <label class="form-check-label">
        <input class="form-check-input" type="checkbox"> Remember me
      </label>
    </div>
    <button type="submit" class="btn btn-primary">로그인</button>
  </form>

</div>

<%@ include file="../layout/footer.jsp"%>
```