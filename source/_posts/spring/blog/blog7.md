---
title: 7. [블로그 프로젝트] 회원가입을 위한 insert 구현하기
date: 2021-09-02 20:29:25
tags:
category:
    - Spring
    - Project
---
## JpaRepository
이제 DB에 insert하기 위해 프로그램을 작성해볼 것이다!
먼저 **com.cos.blog** 패키지 하위에 **repository**라는 폴더를 만들자.
그리고 그 안에 **UserRepository** 인터페이스를 만들자.
이 인터페이스는 DB 저장소에 update할 메소드를 제공한다.
```java
package com.cos.blog.repository;

import com.cos.blog.model.User;
import org.springframework.data.jpa.repository.JpaRepository;

//자동으로 bean으로 등록
//@Repository 생략가능
public interface UserRepository extends JpaRepository<User, Integer> {
    //기본적인 CRUD는 모두 가지고 있음.
}
```
**JpaRepository<\User, Integer>** : <\해당 테이블, 그 테이블의 기본키 객체 형>
이렇게 JpaRepository를 상속해서 만들면 CRUD에 해당하는 메소드를 이용할 수 있다.
그리고 이렇게 처리를 하면 **bean으로 자동으로 등록된다!**


실제로 잘 되는지 한번 테스트 해보자.

## Insert 테스트
우리는 dummyDataController를 만들어 우리가 만든 레포지토리 인터페이스를 실험해보자.


```java
package com.cos.blog.test;

import com.cos.blog.model.RoleType;
import com.cos.blog.model.User;
import com.cos.blog.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DummyControllerTest {

    @Autowired//해당 클래스가 메모리에 올려지면, 해당 변수 타입에 맞는 객체가 메모리에 있으면 변수에 할당.(DI, 의존성 주입)
    private UserRepository userRepository;

    @PostMapping("/dummy/join")
    public String join(String username, String password, String email){
        //x-www.form-urelencoded는 key-value형태로 받는데 이는 스프링이 함수의 파라미터로 밸류값을 다 저장해준다.
        //@RequestParam("username") String u...이런식으로 변수명을 유연하게도 가능!
        System.out.println("username: "+username);
        System.out.println("password: "+password);
        System.out.println("email: "+email);
        return "회원가입이 완료 되었습니다!";
    }
    @PostMapping("/dummy/join2")
    public String join2(User user){
        //객체 형태로도 가능하다!(다만 데이터의 키값이 객체의 변수명과 일치해야 함
        System.out.println("role: "+user.getRole());
        System.out.println("username: "+user.getUsername());
        System.out.println("password: "+user.getPassword());
        System.out.println("email: "+user.getEmail());
        System.out.println("createDate: "+user.getCreateDate());

        user.setRole(RoleType.USER); //User 클래스의 @DynamicInsert를 대신하는 문장.
        userRepository.save(user); //회원정보 저장.
        return "회원가입이 완료 되었습니다!";
    }
}
```
**@Autowired** : 이 어노테이션이 담긴 클래스가 메모리에 올라가면, 이 어노테이션 밑 변수에 형에 맞는 객체를 주입(**DI**)
이 어노테이션을 통해 저장소 객체를 변수에 할당할 수 있다.
그리고 테이블 객체를 **save**메소드로 DB에 저장할 수 있다!