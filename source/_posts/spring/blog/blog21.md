---
title: 21.[블로그 프로젝트] 글 삭제, 수정 구현하기
date: 2021-09-21 17:15:36
tags:
category:
    - Spring
    - Project
---
## 삭제하기
### BoardService에 메소드 추가
```java
    @Transactional
    public void deletePost(int id){
        boardRepository.deleteById(id);
    }
```

### ApiController에 메소드 추가하기
```java
    @DeleteMapping("api/board/{id}")
    public ResponseDto<Integer> deleteById(@PathVariable int id){
        boardService.deletePost(id);
        return new ResponseDto<Integer>(HttpStatus.OK.value(), 1);//정상작동을 알림
    }
```

### board.js로 버튼 눌렸을 때 요청하기.
```js
    deleteById : function(){
        //ajax 호출 시 비동기 호출
        var id = $("#id").text();
        $.ajax({
            type : "DELETE",
            url : "/api/board/"+id,
            dataType: "json"
        }).done(function (resp) {
            alert("글삭제 완료!");
            location.href = "/"; //응답이 정상일때 출력할 링크
        }).fail(function (error) {
            //실패시 수행
            alert(JSON.stringify(error)); //에러도 json객체로 바꿈

        });
    }
```

### detail.js에서 세션 검증 추가
```html
    <c:if test = "${board.user.id == principal.user.id}">
        <button id="btn-delete" class="btn btn-danger">삭제</button>
    </c:if>
```
principal에 저장된 세션 정보와 게시물의 작성자가 동일할 때만 버튼 생성.



## 수정
일단 버튼을 눌렀을 때 입력받을 수 있는 페이지로 넘어가도록 하자
### 컨트롤러에 메소드 추가
```java
    @GetMapping("/board/{id}/updateForm")
    public String updateForm(@PathVariable int id , Model model){
        model.addAttribute("board", boardService.postDetail(id));
        return "board/updateForm";
    }
```
모델에 우리가 고칠 포스트의 내용을 넣고, updateForm 페이지를 띄움

###  updateForm.jsp 구현
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">
    <form>
        <input type="hidden" id="id" value="${board.id}"/>
        <div class="form-group">
            <input value="${board.title}" type="text" class="form-control" placeholder="Enter title" id="title">
        </div>
        <div class="form-group">
            <textarea  class="form-control summernote" rows="5" id="content">${board.content}</textarea>
        </div>
    </form>
    <button id="btn-update" class="btn btn-primary">글수정 완료</button>
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
여기서 버튼을 눌렀을 때 작동할 내용을 스크립트에 적자.

### board.js에 추가하기
```js
let index = {
    init : function(){
        $("#btn-update").on("click",()=>{
            this.update();
        });
    },
    update : function(){
        let id = $("#id").val();
        let data = {
            title: $("#title").val(),
            content: $("#content").val(),
        };
        $.ajax({
            type : "PUT",
            url : "/api/board/"+id,
            data : JSON.stringify(data), //자바가 js 객체를 인식못해서 json으로.
            contentType: "application/json; charset=utf-8", //바디 데이터가 어떤 타입인지
            dataType : "json"
        }).done(function (resp) {//resp에는 json객체가 있겠죠.
            //성공시 수행
            console.log(resp);
            alert("글수정 완료!");
            location.href = "/"; //응답이 정상일때 출력할 링크
        }).fail(function (error) {
            //실패시 수행
            alert(JSON.stringify(error)); //에러도 json객체로 바꿈

        });//ajax 통신을 이용해서 2개의 데이터를 json으로 변경해 insert요청!
    },
```    


### 서비스에 메소드 추가
```java
    @Transactional
    public void updatePost(int id, Board requestBoard){
        Board board = boardRepository.findById(id)
                .orElseThrow(()->{
                    return new IllegalArgumentException("글 수정 실패 : id를 찾지 못함");
                }); //영속성 로드
        board.setTitle(requestBoard.getTitle());
        board.setContent(requestBoard.getContent());
    }
```

### REST 컨트롤러에 메소드 추가
```java
@PutMapping("/api/board/{id}")
    public ResponseDto<Integer> update(@PathVariable int id , @RequestBody Board board){
        boardService.updatePost(id, board);
        return new ResponseDto<Integer>(HttpStatus.OK.value(), 1);//정상작동을 알림
    }
```