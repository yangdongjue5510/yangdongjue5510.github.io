---
title: 16.[블로그 프로젝트] 스프링 시큐리티 로그인
date: 2021-09-13 21:02:53
tags:
category:
    - Spring
    - Project
---
#### 시큐리티는 로그인 요청을 가로채간다.
우리는 회원가입에 필요한 joinProc uri를 UserApiController에 정의했었다.
그러나 로그인에 필요한 메서드는 만들지 않는다.
왜? 시큐리티가 가로채가서 로그인을 실행하기 때문.


#### SecurityConfigure 수정
이제 로그인을 시큐리티가 실행할 수 있게 수정해보자.
```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .csrf().disable() //csrf 토큰 비활성화
                .authorizeRequests()//요청이 왔을 때
                    .antMatchers("/","/auth/**","/js/**","/css/**","/image/**") // /auth로 시작하는건
                    .permitAll() //모두 접근 가능
                    .anyRequest() //이 외의 다른 요청은
                    .authenticated() // 인증해야 접근 가능
                .and() // 인증이 필요한 경우
                    .formLogin() // 로그인 할 수 있도록 한다.
                    .loginPage("/auth/loginForm") //이 로그인 페이지에서
                    .loginProcessingUrl("/auth/loginProc") //시큐리티가 해당 주소로 오는  로그인 가로채고 수행
                    .defaultSuccessUrl("/"); //요청이 성공하고 나서 해당 주소로 이동.
                    //.failureUrl("/auth/loginForm");//실패시 해당 주소로 이동.
    }
```
.and()부터 로그인에 관련된 내용이다!


#### config 패키지 하위에 auth 패키지 만들고 클래스 작성
로그인하고 나면 시큐리티 세션에 사용자 정보가 UserDetails 형으로 저장된다고 했다.
이때 UserDetails는 인터페이스로, 이를 구현한 클래스가 필요하다.


그래서 유저 정보를 담을 PrincipalDetail를 UserDetails의 추상메소드를 구현해서 만들어보자
```java
package com.cos.blog.config.auth;

import com.cos.blog.model.User;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.ArrayList;
import java.util.Collection;

public class PrincipalDetail  implements UserDetails {
    private User user;
    public PrincipalDetail(User user){};

    //계정이 어떤 권한을 가졌는지.
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        Collection<GrantedAuthority> collectors = new ArrayList<>();
        collectors.add(()->{return "ROLE_"+user.getRole();});//role 앞에 ROLE_붙이는건 규칙!!!
        return collectors;
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUsername();
    }

    //계정이 만료됐는지 리턴(true: 만료안됨)
    @Override
    public boolean isAccountNonExpired() {
        return true;
    }
    //계정이 잠겼는지 리턴(true: 안잠김)
    @Override
    public boolean isAccountNonLocked() {
        return true;
    }
    //비밀번호가 만료됐는지 리턴(true: 만료안됨)
    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }
    //계정활성화
    @Override
    public boolean isEnabled() {
        return true;
    }
}
```
이제 로그인된 정보는 PrincipalDetail 객체에 담겨서 UserDetails 형으로 시큐리티 세션에 저장하도록해야 된다.


#### PrincipalDetailService 만들기
이제 해쉬화된 비밀번호가 db에 있다는걸 안다. 근데 입력받은건 그냥 패스워드이다.
입력받은 패스워드를 동일한 해쉬방식으로 해쉬화해야 db에 있는 것과 비교할 수 있다.


일단 유저정보를 username으로 찾아서 반환하는 서비스를 만들어야 한다.
(비밀번호는 시큐리티가 알아서 검사한다.)
username으로 정보를 찾아내는 메소드는 JPA에 없으므로 우리가 만들어야한다.
UserRepository에서 추상메소드를 만들어보자.
```java
    Optional<User> findByUsername(String username);
```


이제 해당 메소드를 활용해서 서비스를 만들어보자.
```java
@Service //Bean등록
public class PrincipalDetailService implements UserDetailsService {
    //username과 password를 가로채서 처리할때
    //password는 알아서 처리하니까
    //username만 DB에 있는지 확인하면 된다.
    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User principal = userRepository.findByUsername(username)
                .orElseThrow(()->{
                    return new UsernameNotFoundException("해당 사용자를 찾을 수 없습니다."+username);
                });
        return new PrincipalDetail(principal); //이때 시큐리티의 유저정보 저장됨! 유저정보가 없다면, user, 콘솔창 비번이 유저정보로 저장될 것이다.
    }
}
```
이제 이 서비스는 유저 이름을 받아서 레포지토리에 있는 유저정보를 찾아와서 PrincipalDetail로 담아서 반환한다!
이제 시큐리티 세션에 PrincipalDetail에 유저정보가 담겨서 로드된다!!


#### SecurityConfig에서 configure 추가하기
이제 유저이름을 통해 유저정보를 찾았으니, 이 유저정보의 암호를 동일한 해쉬로 비교하는 configure메소드를 만들어보자.
```java
    @Autowired
    private PrincipalDetailService principalDetailService;
        @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(principalDetailService).passwordEncoder(encodePWD());
    }
```
이제 암호 해쉬기준도 알려줬으니, 로그인은 시큐리티가 알아서 할 것이다!


> 컨트롤러에서 세션 호출하기

매개변수로 @AuthenticationPrincipal PrincipalDetail principal를 전달해주면됨

```java
   @GetMapping("/")
   public String index2(@AuthenticationPrincipal PrincipalDetail principal) {
       System.out.println("로그인한 사용자 아이디 "+principal.getUsername());
       return "index";
   }
```