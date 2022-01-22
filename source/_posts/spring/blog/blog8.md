---
title: 8. [블로그 프로젝트]select 테스트
date: 2021-09-06 15:12:26
tags:
category:
    - Spring
    - Project
---
### select

#### 1. 하나의 데이터 select하기!
```java
    @GetMapping("/dummy/user/{id}")
    public User detail(@PathVariable int id){
        //1.
        User user = userRepository.findById(id).orElseGet(new Supplier<User>() {
            @Override
            public User get() {
                return new User(); 
            }
        });
        //2.
        User user2 = userRepository.findById(id).orElseThrow(new Supplier<IllegalArgumentException>() {
            @Override
            public IllegalArgumentException get() {
                return new IllegalArgumentException("해당 유저는 없습니다. id :"+id);
            }
        });
        //3.Ramda
        User user3 = userRepository.findById(id).orElseThrow(()->{
            return new IllegalArgumentException("해당 사용자가 없네요. id: "+ id);
        });
        return user2;
```
일단 jpaRepository 메소드인 **findById**를 알아보자.
findById는 id를 받아 **optional 클래스를 반환**한다.(null을 그냥 반환하면 문제가 생길 수도 있기 때문.)
그래서 반환형에 안맞으니, 따로 처리를 더 해주자.


1번은 Optional 클래스의 **orElseGet**메소드로 값이 없는 경우를 처리한다.
이때 orElseGet메소드는 **supplier 인터페이스**를 받으므로, user는 익명 클래스로 get을 오버라이딩해서 처리해줬다.


2번은 Optional 클래스의 **orElseThrow**로 값이 없는 경우에 예외를 발생시킬 수 있다.
3번은 익명 클래스 대신 람다 표현식으로 간단하게 표현했다.


> 자바에서 자바 객체를 전달하는데 웹브라우저가 어떻게..?

자바 객체를 웹브라우저가 이해하려면 json으로 바꿔줘야한다!
스프링 부트는 MessageConverter가 response시 작동해서
Jackson 라이브러리를 호출해서 변환한다!


#### 2. 전체 데이터 select
```java
    @GetMapping("/dummy/users")
    public List<User> list(){
        return userRepository.findAll();
    }
```
JpaRepository 인터페이스의 **findAll메서드**는 모든 데이터를 **리스트** 형식으로 반환한다.
*참 쉽죠?*


#### 3. 페이지로 데이터 select
```java
   @GetMapping("/dummy/user")
    public List<User> list2(@PageableDefault(size = 2, sort = "id",direction = Sort.Direction.DESC)Pageable pageable){
        Page<User> pagingUser = userRepository.findAll(pageable);
        List<User> users = pagingUser.getContent();
        return users;
    }
```
**@PageableDefault**를 붙인 **Pageable** 인터페이스를 인자로 보내준다.
@PageableDefault는 **한 페이지 당 데이터 갯수, 정렬 기준, 정렬 방향(내림차, 오름차)**
findAll메서드에 Pageable인터페이스를 인자로 보내주면, 기준에 맞춘 페이지 객체들을 리스트로 만들어 반환한다.
이 리스트를 getContent하면 우리가 가져오는 데이터 객체를 가져올 수 있다!