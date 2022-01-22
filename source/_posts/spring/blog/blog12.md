---
title: 12.[블로그 프로젝트] Ajax를 활용한 회원가입 세팅
date: 2021-09-08 17:14:35
tags:
category:
    - Spring
    - Project
---
#### JS 세팅
resource - static에 js폴더를 만들고 그 안에 user폴더 생성한다.
user폴더에 user.js를 만들고 다음같이 작성한다.
그리고 jQuery를 사용해 다음같이 작성한다.
```js
let index = {
    init : function(){
        $("#btn-save").on("click",()=>{
            this.save();
        });//btn-save를 찾아 리스너를 킨다.
    },
    save : function(){
        alert('user의 함수 호출');
    }
}
index.init();
```
그리고 joinForm.jsp에 가서 button태그가 form에 있으면 submit이 같이 되기 때문에 button태그 바깥으로 빼준다.
그리고 버튼 태그의 type 속성을 지우고, id = "btn-save"를 넣어준다.
그리고 스크립트 태그로 우리가 만든 user.js 태그를 사용해서 연결한다.
```html
    </form>
    <button id="btn-save" class="btn btn-primary">회원가입</button>

</div>
<script src = "/blog/js/user.js"></script>
```


이제 joinForm.jsp에서 스크립트 태그를 읽게되면, js파일의 init을 호출하게 되고, init은 save함수를 호출하게 되므로 user의 함수 호출이라는 알림이 뜨게 된다!


그러고 나서 user.js를 다음같이 데이터를 받아오도록 하고 브라우저 개발자 도구의 콘솔로 확인해볼 수 있도록 해보자.
```js
let index = {
    init : function(){
        $("#btn-save").on("click",()=>{
            this.save();
        });//btn-save를 찾아 리스너를 킨다.
    },
    save : function(){
        let data = {
            username: $("#username").val(),
            password: $("#password").val(),
            email: $("#email").val()
        }
        console.log(data);
    }
}
index.init();
```


#### Ajax 요청으로 회원가입 처리하기
Ajax를 왜 사용할까? [링크](https://yangdongjue5510.github.io/2021/09/08/Spring/boot/boot7/)


user.js에 Ajax로 다음과 같이 작성하자.
```js
let index = {
    init : function(){
        $("#btn-save").on("click",()=>{
            this.save();
        });//btn-save를 찾아 리스너를 킨다.
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
    }
}
index.init();
```
이 코드를 해석하면
this.init은 btn-save를 가진 태그가 클릭되는지 감시하는 리스너를 활성화한다.
클릭이 되면 this.save를 호출한다.


this.save는 회원가입 페이지에서 입력한 3가지 변수를 가져와 js객체에 할당하고, Ajax로 서버에 회원가입을 요청한다.


> Ajax의 요청할 때 코드는 이런 의미다.
어떤 종류의 요청인지?
어떤 url로 요청할 것인가?(js오브젝트를 json으로)
body에 어떤 데이터를 담아 보낼 것인가?
body에는 어떤 형식의 데이터를 담았는가?
응답을 받을 때 어떤 형식으로 오는가?


그리고 나서 성공했을 때의 done과 실패했을 때의 fail을 작성했다.
done은 완료 메시지를 브라우저가 표시하고, 메인화면을 표시하도록 한다.
fail은 자바 객체로 받은 error를 json으로 바꿔 브라우저에 표시한다.


#### UserApiController 클래스
이제 controller 패키지에 api 패키지를 만들고
UserApiController 클래스를 만들어 메소드를 만들자.
```java
@RestController
public class UserApiController {

    @Autowired
    private UserService userService;

    @PostMapping("/api/user")
    public ResponseDto<Integer> save(@RequestBody User user){
        System.out.println("UserApiController save called");
        user.setRole(RoleType.USER);
        userService.signUp(user);
        return new ResponseDto<Integer>(HttpStatus.OK.value(), 1);//정상작동을 알림
    }
}
```
여기에는 dto와 service 개념이 필요한데,
일단 dto 패키지를 만들어 ResponseDto클래스를 만들자.
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ResponseDto <T>{
    int  status;
    T data;
}
```
이 객체는 데이터와 http 상태 코드를 저장하는 변수가 있다.


그리고 데이터베이스 트랜잭션을 담당하는 service는 [링크](https://yangdongjue5510.github.io/2021/09/08/Spring/boot/boot8/)를 참고하자.
서비스도 service 패키지를 만들어서 UserService 클래스를 만들자.
```java
//스프링이 컴포넌트 스캔을 통해서 Bean에 자동 등록. IoC를 해줌
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    @Transactional //메소드 속 여러 트랜잭션이 하나의 트랜잭션을 묶임
    public void signUp(User user){
        userRepository.save(user);//잘못된 예외발생시 GlobalExceptionHandler가 예외처리
    }
}
```
레포지토리를 DI로 가져와서, signUp메소드 속 트랜잭션들을 묶어서 처리한다.




#### 요약
이제 회원가입 페이지에 들어가면 user.js에 따라 버튼에 이벤트 리스너가 켜지고,
내용을 넣고 회원가입 버튼을 누르면, 내용을 js객체로 담고 Ajax로 json으로 바꿔서 url로 요청한다.


UserApiController에 있는 useService서비스를 실행시켜 데이터를 DB에 저장하고 ResponseDto에 http상태와 성공유무 알게 하는 정수 값을 반환한다.
요청 성공/실패에 따라, 브라우저가 메시지를 나타낸다!