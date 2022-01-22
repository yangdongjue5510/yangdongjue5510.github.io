---
title: 6. [블로그 프로젝트] 데이터 테이블 생성 및 관계 설정.
date: 2021-09-01 14:26:09
tags:
category:
    - Spring
    - Project
---
## application.yml의 JPA
일단 **application.yml**파일에 있는 jpa 관련 내용을 해석해보자.
```yml
  jpa:
    open-in-view: true
    hibernate:
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
      use-new-id-generator-mappings: false
      ddl-auto: create
    show-sql: true
    properties:
      hibernate.format_sql: true
```
**use-new-id-generator-mappings** : true면 JPA가 사용하는 기본 넘버링 전략을 사용한다는 의미!
**ddl-auto** : 매 서버를 시작할 때마다 있던 테이블은 지우고, 새로 만들거나(create), 있던 걸 업데이트(update)
**show-sql** : 서버 콘솔에 쿼리를 표시
**hiberate.format_sql** : 쿼리가 정렬되서 나오도록 표시
**physical-strategy** : DB에 이름을 어떻게 저장할 것인가. (현재는 자바 객체 이름 그대로)

## 데이터 테이블 만들기
현 프로젝트의 블로그의 댓글을 위해 user테이블, board테이블, reply테이블을 만들자.
JPA로 데이터를 전달하기 위해, User, Board, Reply 클래스를 만들어서 
각 속성들을 private로 선언한다.
이제 각 어노테이션에 대해 알아보자.


```java
package com.cos.blog.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.ColumnDefault;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.DynamicInsert;

import javax.persistence.*;
import java.sql.Timestamp;

//lombok
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity //User 클래스가 mysql에 테이블이 생성된다.
//@DynamicInsert //null인 속성은 insert에서 제외한다.(null이 아닌 default값으로 채우겟다.) 어노테이션에 의존하기보다 로직으로 해결하자.
public class User {
    @Id//primary key
    @GeneratedValue(strategy =  GenerationType.IDENTITY) //넘버링 전략, 프로젝트에 연결된 db의 넘버링 전략을 따라(identity전략)
    private int id; //시퀀스, auto-increment

    @Column(nullable = false, length = 30)
    private String username;

    @Column(nullable = false, length =  100) //해쉬때문에 넉넉히 잡음
    private String password;

    @Column(nullable = false, length = 50)
    private String email;

    //@ColumnDefault("'user'") //" 'user' "로 써야함!
    //private String role; //Enum을 쓰는게 좋지만, 지금은 String사용.

    @Enumerated(EnumType.STRING)//DB에는 RoleType이란게 없으니까 이 속성이 문자열임을 알
    private RoleType role; //Enum을 쓰는 예시. ADMIN, USER

    @CreationTimestamp //시간이 자동 입력됨.
    private Timestamp createDate;
}
package com.cos.blog.model;

public enum RoleType {
    USER, ADMIN
}
```
**@Entity** : 해당 클래스를 JPA에서 테이블로 다루겠다!
**@Id** : 해당 변수를 기본키로 설정
**@GeneratedValue(strategy = GenerationType.IDENTITY)** : 프로젝트에 연결된 DB의 넘버링 전략을 그대로 사용한다.(따로 입력안해도 된다. 자동으로 할당)
**@Column(nullable = false, length = 10)** : null을 허용하지 않고 길이가 10이하인 속성 선언
**@ColumnDefault("~")** : 속성의 기본값을 설정 (문자열은 "'문자열'"로 해줘야!)
**@Enumerated(EnumType.STRING)** : 변수가 enum일때, DB에 저장될 형식 선언
**@CreationTimestamp** : 시간을 생성해 변수에 할당.

```java
package com.cos.blog.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.ColumnDefault;
import org.hibernate.annotations.CreationTimestamp;

import javax.persistence.*;
import java.sql.Timestamp;
import java.util.List;

//lombok
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false, length = 100)
    private String title;

    @Lob //대용량 데이터를 다룰 때 사용
    private String content; //섬머노트 라이브러리 <html>태그가 섞여서 디자인이 된다.

    @ColumnDefault("0") //default value
    private int count;

    //자바에서 객체를 저장하면, DB에서는 FK로 구현된다.
    //객체를 다루려면, 관계를 설정해줘야 한다.
    @ManyToOne(fetch = FetchType.EAGER) //Many = Board, One = User 한 유저가 여러 게시물을 작성 가능한 관계.
    //fetch 전략이 해당 클래스를 가져오면 반드시 가져온다.(게시물을 불러오면 user를 반드시 조인해서 불러와라!)
    @JoinColumn(name = "userId")//데이터베이스는 객체를 저장할 수 없으므로 userId라는 값이 대신 테이블에 저장된다.
    private User user; //DB에서는 오브젝트를 저장할 수 없다. 그래서 FK를 사용. 반면 자바는 오브젝트를 저장할 수 있다.

    @OneToMany(mappedBy = "board", fetch = FetchType.EAGER)
    //LAZY : board를 불러온다고 무조건 가져올 필요는 없다!(기본값으로 설정되어잇음)
    //EAGER : 게시물이 불러오면 무조건 조인해서 가져와야됨!
    private List<Reply> reply;

    @CreationTimestamp
    private Timestamp createDate;
}
```
**@Lob** : 대용량 데이터를 사용할 때 사용.
**@ManyToOne** : 관계를 설정(현재 클래스 to 대상 클래스)
**fetch = FetchType.EAGER** : 해당 클래스를 가져올 때 조인해서 같이 가져오도록함
**fetch = FetchType.LAZY** : 해당 클래스를 가져올 때 반드시 가져올 필요는 없음
**@JoinColumn(name = "~")** : 데이터베이스에 객체로 저장이 안되므로, 저장되는 속성이름을 지정
**mappedBy = "~"** : 이건 외래키를 가진 객체가 해당 속성의 주인(즉 외래키를 가진 DB에 해당 속성이 있다는 의미. 단지 객체지향적 의미로 해당 클래스에 리스트로 객체들이 선언되있음.)


```java
package com.cos.blog.model;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.hibernate.annotations.CreationTimestamp;

import javax.persistence.*;
import java.sql.Timestamp;

//lombok
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
public class Reply {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;

    @Column(nullable = false, length = 200)
    private String content;

    @ManyToOne//Many reply to One board
    @JoinColumn(name="boardId")
    private Board board;

    @ManyToOne
    @JoinColumn(name = "userId")
    private User user;

    @CreationTimestamp
    private Timestamp createDate;
}
```
여기보면 Reply가 BoardId 외래키로 가지고 있다.
