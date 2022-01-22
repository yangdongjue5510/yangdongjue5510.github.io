---
title: 3. [블로그 프로젝트] 스프링 부트로 HTTP 체험해보기
date: 2021-08-26 15:39:51
tags:
category:
    - Spring
    - Project
---
> HTML을 체험하기 위해 필요한 POSTMAN

추후 알게 되겠지만, POSTMAN이라는 프로그램을 다운받자.
(브라우저로 테스트하면 get요청밖에 확인가능하기 때문이다.)
일단은 웹브라우저로 해보자.


## @RestController 와 @Controller
@RestController는 사용자가 요청하면 데이터를 응답하는 어노테이션이다.
@Controller는 사용자가 요청하면 HTML을 응답하는 어노테이션이라고 알자!


알맞은 패키지 아래에
이제 BlogControllerTest 클래스를 만들어보자.
```java
package com.cos.blog.test;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class BlogControllerTest {
    @GetMapping("/test/hello")
    public String hello() {
        return "<h1>hello spring boot</h1>";
    }
}
```
이제 웹 브라우저 주소창에 http://localhost:8080/test/hello 를 입력하면 
반환값을 html로 해석한 페이지가 등장한다!


***

이제 다양한 요청을 처리하는 HttpControllerTest를 만들어보자
```java
package com.cos.blog.test;

import org.springframework.web.bind.annotation.*;

@RestController
public class HttpControllerTest {

    @GetMapping("/http/get")
    public String getTest(){
        return "get";
    }
    @PostMapping("/http/post")
    public String postTest(){
        return "post";
    }
    @PutMapping("/http/put")
    public String putTest(){
        return "put";
    }
    @DeleteMapping("/http/delete")
    public String deleteTest(){
        return "delete";
    }
}
```
이제 http://localhost:8080/http/get 을 하면 get이라는 글자가 화면에 나온다!(와우!)
근데 http://localhost:8080/http/post 를 해보면...?(어라?)
![브라우저는_get요청만_처리!](/img/boot/blog/blog3-1.png)
에러가 뜬다!
앞서 언급했듯이 이게 우리가 POSTMAN을 쓰는 이유다!
이제 앞으로 포스트맨으로 결과를 확인하자!!
![포스트맨으로post](/img/boot/blog/blog3-2.png)