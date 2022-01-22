---
title: 4. [블로그 프로젝트] 스프링 부트로 HTTP 데이터 전달하기
date: 2021-08-26 16:08:56
tags:
category:
    - Spring
    - Project
---
## HTTP는 어떻게 데이터를 전달할까?


> GET

get은 URI의 쿼리 파라미터로 데이터를 전달한다.
(? 뒤의 쿼리를 의미한다.)
코드로 받아오는 방법은 두가지가 있다.


1. @RequestParam : 메소드에 해당 어노테이션을 붙인 변수를 만들어서 받아온다!
2. 클래스를 만들어서 가져오기 : 메소드에 해당 클래스 객체를 넣어주면 알아서 매핑되서 받아온다.
**이때 변수 명이나, URI의 key값이 클래스 멤버 변수와 동일해야 한다!!!**
```java
    @GetMapping("/http/get")
    //public String getTest(@RequestParam int id, @RequestParam String username...)
    public String getTest(Member m){
        return "get id :" + m.getId() + ", username: "+m.getUsername()+", password : "+m.getPassword()+", email : "+m.getEmail();
    }
```


> GET 외의 api들(POST, PUT ...)

get을 제외한 api들은 다양한 데이터를 전달할 수 있다.
그렇다면 어떻게 데이터를 보낼까?
바로 **body**에 담아서 보낸다!!


그런데, body에서 전달하는 방식도 여러가지가 있다.
포스트맨에서 확인해보자.
![](/img/boot/blog/blog4-1.png)


1. **x-www.form-unlencoded**
여기 보이는 **x-www.form-unlencoded**는 HTML에서 보이는 form태그 방식으로 데이터를 보낸다.
이럴 경우에는 우리 코드 상의 메소드의 매개변수를 그냥 객체 형식으로 보내도 상관없다.
form을 전제로 하기 때문에 자연스럽게 파싱된다.


2. **raw**
하지만 **raw**는 그렇지 않다. 우리 코드상의 메소드의 매개변수 앞에 **@RequestBody**를 붙여줘야 파싱한다.
이때 raw도 그냥 raw가 아니라 다양한 MIME형식을 가질 수 있다.
(text/plain , application/json)

```java
    @PostMapping("/http/post")
    public String postTest(@RequestBody Member m){ //json받기
//  public String postTest(@RequestBody String text){ //text받기
        return "post id :" + m.getId() + ", username: "+m.getUsername()+", password : "+m.getPassword()+", email : "+m.getEmail();
        //return text;
    }
```