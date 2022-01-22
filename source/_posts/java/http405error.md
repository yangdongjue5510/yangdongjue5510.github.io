---
title: 405 Not Allowed 해결하기
date: 2021-11-01 14:25:13
tags: troubleShooting
category: 
    - Java
---

# 405 Not Allowed

Http status code 중 405는 특정 Http method가 혀용되지 않는다는 의미다.

## 문제 배경

서블릿 프로젝트에서 입력 페이지에서 데이터를 입력받아 post로 데이터를 넘겨주고 
해당 데이터가 유효하면 다른 url로 리다이렉트 하고,
그렇지 않으면 다시 입력 페이지에서 데이터를 입력받도록 구성했는데 405 문제가 생겼다.



## 문제가 발생하는 이유와 증상들

### 1. 데이터 유효에 따라 다르게 발생

일단 유효한 데이터를 입력해서 post 요청하면 문제가 발생하지 않는데,
유효하지 않은 데이터를 입력하는 경우 이런 문제가 발생했다.

### 2. 무한 반복되는 로그

post대신 get으로 로직을 짜도 문제가 발생했다.

유효하지 않은 데이터를 입력받으면 리다이렉션이 너무 많이 일어나는 에러가 발생했다.

로그를 살펴보니 끝없이 데이터의 유효성 검사를 하고 있었다.
분명, 다시 데이터를 입력받아야 정상 로직인데, 입력을 받지도 않고 유효성 검사를 무한 반복하고 있었다.

### 3. 같은 URL로 요청 두 개가 전달.

또한 브라우저에서 개발자 모드로 살펴보니, 내가 의도한 url로 post 요청과 get 요청 두개가 요청되고 있었다.
post는 내가 의도한 게 맞는데, get은 내가 의도하지 않은 요청이다.



## 문제 원인

데이터의 유효값을 체크하여 리다이렉트 하는 곳에서 문제가 있었다.

```java
@WebServlet(name = "LoginServlet", value = "/login.do")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      ....
      //데이터 유효성 확인 후 리다이렉트
      if(user!= null) {
        response.sendRedirect("/ServletProject2_war_exploded/board.do/list");
      }else{
        response.sendRedirect("login.do");
      }
    }
}
```

이게 문제가 되는 코드다.

데이터가 유효하지 않으면 login.do로 리다이렉트하고 있다.
이러면 login.do로 get요청을 하게된다.

그런데 나는 login.do를 처리하는 LoginServelt 클래스에 post만 처리 할 수 있도록 했다.
405 code는 이런 상황에서 발생한 것이다.

그리고 get으로 로직을 다시 짰을 때 무한 리다이렉션이 일어나는 이유는
login.do로 유효하지 않은 데이터 받음 -> 유효하지 않으니 login.do get요청 -> login.do로 빈 쿼리파라미터 받음-> 유효하지 않으니 login.do get 요청...

이렇게 무한 반복되서 그랬다.



## 문제 해결

다음과 같이 코드를 고치면 된다.

```java
@WebServlet(name = "LoginServlet", value = "/login.do")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
      ....
      //데이터 유효성 확인 후 리다이렉트
      if(user!= null) {
        response.sendRedirect("/ServletProject2_war_exploded/board.do/list");
      }else{
        response.sendRedirect("login.html");
      }
    }
}
```



