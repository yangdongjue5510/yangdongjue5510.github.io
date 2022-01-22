---
title: 13.[블로그 프로젝트] 전통적인 로그인 구현
date: 2021-09-13 13:33:12
tags:
category:
    - Spring
    - Project
---
#### loginForm.jsp
회원가입을 구현했던 것과 비슷하게 진행한다.
loginForm.jsp에서 button 태그를 form 밖으로 빼고, 스크립트 태그를 추가해 user.js를 읽을 수 있게 했다.
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">
  <form >
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
  </form>
  <button id="btn-login" class="btn btn-primary">로그인</button>
</div>
<script src = "/blog/js/user.js"></script>
<%@ include file="../layout/footer.jsp"%>
```

#### user.js
이제 user.js에 로그인 버튼이 눌렸을 때 액션을 만들어서 추가해줘야 한다. 이전에 만든 save와 비슷하게 만든다.
```js
let index = {
    init : function(){
        $("#btn-save").on("click",()=>{
            this.save();
        });//btn-save를 찾아 리스너를 킨다.
        $("#btn-login").on("click",()=>{
            this.login();
        });//btn-login을 찾아 리스너를 킨다.
    },
    save : function(){
        let data = {
            username: $("#username").val(),
            password: $("#password").val(),
            email: $("#email").val()
        };
        //console.log(data);
        //ajax 호출 시 비동기 호출
        $.ajax({
            //회원가입을 요청
            type : "POST",
            url : "/blog/api/user",
            data : JSON.stringify(data), //자바가 js 객체를 인식못해서 json으로.
            contentType: "application/json; charset=utf-8", //바디 데이터가 어떤 타입인지
            dataType : "json" //서버가 보낸 응답이 왔을때 생긴게 json이면 js오브젝트로 변환해서 parse!
        }).done(function (resp) {//resp에는 json객체가 있겠죠.
            //성공시 수행
            console.log(resp);
            alert("회원가입완료");
            location.href = "/blog"; //응답이 정상일때 출력할 링크
        }).fail(function (error) {
            //실패시 수행
            alert(JSON.stringify(error)); //에러도 json객체로 바꿈

        });//ajax 통신을 이용해서 3개의 데이터를 json으로 변경해 insert요청!
    },
    login : function(){
        let data = {
            username: $("#username").val(),
            password: $("#password").val(),
        };
        //ajax 호출 시 비동기 호출
        $.ajax({
            //로그인을 요청
            type : "POST",
            url : "/blog/api/user/login",
            data : JSON.stringify(data), //자바가 js 객체를 인식못해서 json으로.
            contentType: "application/json; charset=utf-8", //바디 데이터가 어떤 타입인지
            dataType : "json" //서버가 보낸 응답이 왔을때 생긴게 json이면 js오브젝트로 변환해서 parse!
        }).done(function (resp) {//resp에는 json객체가 있겠죠.
            //성공시 수행
            console.log(resp);
            alert("회원가입완료");
            location.href = "/blog"; //응답이 정상일때 출력할 링크
        }).fail(function (error) {
            //실패시 수행
            alert(JSON.stringify(error)); //에러도 json객체로 바꿈

        });//ajax 통신을 이용해서 3개의 데이터를 json으로 변경해 insert요청!
    }
}
index.init();
```

#### UserRepository
이제 UserService 서비스에 login 트랜잭션을 만들어야되는데, userRepository에 회원확인 할  수 있는 메소드를 만들어야 한다!!!


> JPA Naming 쿼리

메소드 이름을 통해 쿼리를 작성하는 효과를 얻기.
```java
//JPA Naming query
    //SELECT * FROM user WHERE username = ? AND password = ?
    User findByUsernameAndPassword(String username, String password);
```
이런 식으로 작성하면 우리가 원하는 쿼리와 동일한 역할을 하는 메소드를 만들 수 있다.    


> 네이티브 쿼리

JPA Naming 쿼리와 같은 효과를 네이티브 쿼리로도 얻을 수 있다.
```java
    @Query(value = "SELECT * FROM user WHERE username = ?1 AND password = ?2", nativeQuery = true)
    User login2(String username, String password);
```
이런 방식은 쿼리가 복잡해질 때 유리하다.


#### 서비스
이제 우리에게 필요한 쿼리를 만들었으니 서비스에서 적용시켜보자.
```java
@Transactional(readOnly = true)
public User login(User user){
    return userRepository.findByUsernameAndPassword(user.getUsername(), user.getPassword());
}
```
readOnly = true는 select 할 때 트랜잭션이 시작되고, 서비스 종료될 때 트랜잭션도 종료해서 정합성을 유지하는 역할을 한다.


#### 컨트롤러 수정
```java
    @PostMapping("/api/user/login")
    public ResponseDto<Integer> login(@RequestBody User user, HttpSession session){
        System.out.println("UserApicontroller : login called");
        User principal =userService.login(user);
        if (principal != null){
            session.setAttribute("principal", principal);
        }
        return new ResponseDto<Integer>(HttpStatus.OK.value(), 1);
    }
```
컨트롤러도 HTTPSession을 추가했다.
로그인에 성공하면 세션에 principal를 추가한다.
이 속성으로 서버가 사용자를 추측할 수 있게한다.


그리고 HTTPSession을 private로 따로 빼서 @Autowired해도 된다. 그러면 함수 인자로 전달안해도 된다.

#### JSTL 라이브러리 추가하기
이제 로그인 완료한 사용자의 헤더에는 로그인이 뜨면 안된다.
이걸 처리하기 위해서 세션에 principal 속성을 확인하고, 이에 따라 분기 처리를 해줘야 한다.


따라서 JSTL의 태그 라이브러리를 사용할 것이다.
```html
<%@ taglib prefix = "c" uri = "http://java.sun.com/jsp/jstl/core" %>
```
이 코드를 header.jsp 맨위에 붙여놓고, 서버를 실행한다.
그 이후, pom.xml에 가서 JSTL dependency가 잘 설정됐는지 확인한다.


#### header.jsp 고치기
JSTL의 태그 라이브러리에 따르면,
```html
<c:choose>
    <!-- if문 -->
    <c:when test=""> </c:when>
    <!-- else문 -->
    <c:otherwise> </c:otherwise>
</c:choose>
```
이런식으로 jsp 안에 조건문을 사용할 수 있다.
```html
 <c:choose>
            <c:when test=""></c:when>
        </c:choose>
        <c:choose>
            <c:when test="${empty sessionScope.principal}">
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" href="/blog/user/loginForm">로그인</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/blog/user/joinForm">회원가입</a>
                    </li>
                </ul>
            </c:when>
            <c:otherwise>
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" href="/blog/board/writeForm">글쓰기</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/blog/user/userForm">회원정보</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="/blog/user/logout">회원정보</a>
                    </li>
                </ul>
            </c:otherwise>
        </c:choose>
```


> 지금까지 전통적 로그인을 구현했다.

하지만 스프링에는 스프링 시큐리티를 이용하면 더 쉽게 구현 가능하다.(시큐리티를 이용할꺼면 만든거 주석처리해주자.)