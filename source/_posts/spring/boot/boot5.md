---
title: 5. 스프링 기본파싱전략과 JSON통신
date: 2021-09-07 17:17:14
tags:
category:
    - Spring
    - Boot
---
#### Get요청
Get은 body에 데이터를 못 담는다.
**주소**에 데이터를 담아 보낸다.
데이터 형태는 **key : value** 형태이다.

#### Post, Put, Delete 요청
데이터를 담아보내야 할 것이 많다!
**body**에 담아서 보낸다.


form 태그 방식으로 post를 요청할 수 있지만,
form은 get, post만 가능하므로
자바스크립트로 요청하는게 일반적이다.


결국 자바스크립트로 ajax 요청 + 데이터는 json으로 통일해서 요청한다!


근데 스프링에는 form:form 태그가 있다.
put, post, delete, get이 모두 가능하다.(그냥 있다고만 알자.)


#### 스프링 컨트롤러의 기본 파싱전략(key : value 형태의 데이터, form태그 형식)
스프링 컨트롤러는 기본적으로 key : value 형태로 파싱해서 변수에 할당한다.
스프링은 key : value 형태 데이터를 자바 오브젝트로 파싱해서 받아주기도 한다.
*해당 자바 오브젝트에 setter가 없으면, key : value 데이터를 파싱하지 못한다.*
*즉 오브젝트에 없는 요소를 넘겨줘도 문제가 일어난다는 의미다.*


#### key : value 형식이 아닌 데이터는 어떻게 파싱할까?
json이나 text데이터를 스프링 컨트롤러가 받기 위해서는
**@RequestBody** 어노테이션이 필요하다!!!
@RequestBody를 붙이면 MessageConverter 클래스를 구현한 jackson라이브러리가 발동되면서
데이터를 **자바 오브젝트**로 파싱해 받아온다.
