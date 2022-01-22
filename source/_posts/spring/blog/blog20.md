---
title: 20.[블로그 프로젝트] 게시물 상세보기
date: 2021-09-21 16:22:05
tags:
category:
    - Spring
    - Project
---
### BoardService에 postDetail 추가하기
```java
    public Board postDetail(int id){
        return boardRepository.findById(id)
                .orElseThrow(()->{
                    return new IllegalArgumentException("글 상세보기 실패 : id를 찾지 못함");
                });
    }
```
findById를 사용하면 쉽게 처리할 수 있다~

### 컨트롤러에 메소드 추가
```java
    @GetMapping("/board/{id}")
    public String findById(@PathVariable int id, Model model){

        model.addAttribute("board", boardService.postDetail(id));
        return "board/detail";
    }
```
borad/detail.jsp 파일을 반환해야 한다.

### index.jsp 수정하기
```html
<div class="container">
    <c:forEach var="board" items="${boards.content}">
        <div class="card m-2" >
            <div class="card-body">
                <h4 class="card-title">${board.title}</h4>                
                <a href="/board/${board.id}" class="btn btn-primary">상세보기</a>
            </div>
        </div>
    </c:forEach>
```
상세보기를 하이퍼링크로 연결하도록 하자.

### detail.jsp 만들기
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ include file="../layout/header.jsp"%>

<div class="container">

    <button class="btn btn-secondary" onclick="history.back()">돌아가기</button>
    <button id="btn-update" class="btn btn-warning">수정</button>
    <button id="btn-delete" class="btn btn-danger">삭제</button>
    <br />

    <div>
        <h3>${board.title}</h3>
    </div>
    <hr/>
    <div>
        <div>${board.content}</div>
    </div>
    <hr/>
</div>

<script src = "/js/board.js"></script>
<%@ include file="../layout/footer.jsp"%>
```
돌아가기, 수정, 삭제 버튼을 만들고, 모델의 글 제목과 글 내용을 가져온다.
(아직 수정과 삭제를 구현하지 않았다. 추후에 해보자.)