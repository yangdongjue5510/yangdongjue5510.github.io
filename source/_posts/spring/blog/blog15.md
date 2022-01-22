---
title: 15.[블로그 프로젝트] 비밀번호 해쉬화 해서 회원가입
date: 2021-09-13 16:37:23
tags:
category:
    - Spring
    - Project
---
#### 시큐리티는 어떤 원리로 작동할까?
스프링에서 로그인 요청을 하면 시큐리티가 이를 지켜보다가
username과 password를 가로채간다.
시큐리티는 가로챈 username과 password로 로그인을 진행시키고,
시큐리티 세션에 해당 유저 정보 객체를 저장하고, DI하여 사용하도록 한다.
(이때, 세션에 저장된 유저 정보는 **UserDetails** 객체다.)


그러면 우리가 다루려는 객체 형과 UserDetails가 안맞는 경우가 생긴다.
(ex. User != UserDetails)
-> 우리가 다루려는 객체 형이 UserDetails를 상속
-> (User extends UserDetails)


또한 시큐리티가 로그인을 진행할 때, 그냥 비밀번호 자체를 주면 안된다. 해쉬화 해서 진행해야 로그인이 된다.
(DB에도 비밀번호를 해쉬값을 저장한다.)


#### SecurityConfig에서 인코더 객체 Bean등록.
```java
    @Bean //IoC / BCryptPasswordEncoder객체를 스프링이 관리
    public BCryptPasswordEncoder encodePWD(){
        return new BCryptPasswordEncoder();
    }
```


#### UserService 수정
BCryptPasswordEncoder 객체를 DI하고,
encode 메서드로 주어진 값을 해쉬화한다!
```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;
    @Autowired
    private BCryptPasswordEncoder encoder;

    @Transactional //메소드 속 여러 트랜잭션이 하나의 트랜잭션을 묶임
    public void signUp(User user){
        String rawPassword = user.getPassword();
        String encPassword = encoder.encode(rawPassword);
        user.setRole(RoleType.USER);
        user.setPassword(encPassword);
        userRepository.save(user);//잘못된 예외발생시 GlobalExceptionHandler가 예외처리
    }
}
```