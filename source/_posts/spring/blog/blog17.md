---
title: 17.[블로그 프로젝트] 글쓰기 구현
date: 2021-09-14 15:55:01
tags:
category:
    - Spring
    - Project
---
#### BoardController
BoardController에 글쓰기 버튼을 눌렀을때 분기처리 메소드를 만들자.
```java
    //USER 권한 필요
    @GetMapping("/board/saveForm")
    public String saveForm(){
        return "board/saveForm";
    }
```

#### 화면 구성
그리고 views폴더에 board 디렉토리를 만들고 saveForm.jsp를 만들자.
부트스트랩에서 타이틀과 textarea를 가져오고
글작성 템플릿을 위해 서머노트를 사용하자.


header.jsp에서 summernote를 임포트하자!
head태그에 다음 코드를 넣어주자.
```html
    <%--    summernote--%>
    <link href="https://cdn.jsdelivr.net/npm/summernote@0.8.18/dist/summernote-bs4.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/summernote@0.8.18/dist/summernote-bs4.min.js"></script>
```
그리고 부트스트랩과 서머노트를 적용한 saveForm을 작성하자.
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">
    <form>
        <div class="form-group">
            <label for="title">Title</label>
            <input type="text" class="form-control" placeholder="Enter title" id="title">
        </div>
        <div class="form-group">
            <label for="content">Content:</label>
            <textarea class="form-control summernote" rows="5" id="content"></textarea>
        </div>
    </form>
    <button id="btn-save" class="btn btn-primary">글쓰기 완료</button>
</div>
<%--summernote--%>
<script>
    $('.summernote').summernote({
        tabsize: 2,
        height: 300
    });
</script>
<script src = "/js/board.js"></script>
<%@ include file="../layout/footer.jsp"%>
```
글쓰기 완료 버튼이 form 바깥에 둠으로써, 글 데이터를 폼데이터가 아닌 json데이터로 전달한다는 걸 알 수 있다.

#### 글작성 버튼 누르면 작동되는 js파일 구현
이제 board.js를 작성해서, 글내용이 json으로 변환되서 전달해야 한다.
```js
let index = {
    init : function(){
        $("#btn-save").on("click",()=>{
            this.save();
        });//btn-save를 찾아 리스너를 킨다.
        // $("#btn-login").on("click",()=>{
        //     this.login();
        // });//btn-login을 찾아 리스너를 킨다.
    },
    save : function(){
        let data = {
            title: $("#title").val(),
            content: $("#content").val(),
        };
        //console.log(data);
        //ajax 호출 시 비동기 호출
        $.ajax({
            //글쓰기를 요청
            type : "POST",
            url : "/api/board",
            data : JSON.stringify(data), //자바가 js 객체를 인식못해서 json으로.
            contentType: "application/json; charset=utf-8", //바디 데이터가 어떤 타입인지
            dataType : "json" //서버가 보낸 응답이 왔을때 생긴게 json이면 js오브젝트로 변환해서 parse!
        }).done(function (resp) {//resp에는 json객체가 있겠죠.
            //성공시 수행
            console.log(resp);
            alert("글쓰기 완료!");
            location.href = "/"; //응답이 정상일때 출력할 링크
        }).fail(function (error) {
            //실패시 수행
            alert(JSON.stringify(error)); //에러도 json객체로 바꿈

        });//ajax 통신을 이용해서 2개의 데이터를 json으로 변경해 insert요청!
    }
}
index.init(); 
```
이제 버튼이 클릭되면, 아약스로 **/api/board**로 json 데이터를 보낸다.
그러면 해당 uri에 맞는 분기처리를 해줘야 겠다!
작성된 글을 데이터베이스에 저장하려면 사용자 정보, DB 레포지토리, 글작성 서비스가 필요하다.
이제 JPA로 레포지토리를 만들어보자.


#### JPA레포지토리로 Board 테이블 연결하기
```java
public interface BoardRepository extends JpaRepository<Board, Integer> {
    //기본적인 CRUD는 모두 가지고 있음.

}
```

#### 글작성 서비스 만들기
```java
@Service
public class BoardService {
    @Autowired
    private BoardRepository boardRepository;
    @Transactional
    public void writePost(Board board, User user){
        board.setCount(0);
        board.setUser(user);
        boardRepository.save(board);
    }
}
```
서비스에 글작성 트랜잭션을 구현했다.
이제 분기처리를 해보자.


#### BoardApiController로 분기처리
```java
@RestController
public class BoardApiController {
    @Autowired
    private BoardService boardService;

    @PostMapping("/api/board")
    public ResponseDto<Integer> save(@RequestBody Board board, @AuthenticationPrincipal PrincipalDetail principal){
        boardService.writePost(board, principal.getUser());

        return new ResponseDto<Integer>(HttpStatus.OK.value(), 1);//정상작동을 알림
    }
}
```
save메소드는 바디로 글 정보를 얻고, 시큐리티 세션에 있는 PrincipalDetail을 매개변수를 받아왔다.
이를 통해 트랜잭션 메소드에 글내용과 글쓴 유저정보를 인수로 담아 보내고,
그 결과를 반환한다!



