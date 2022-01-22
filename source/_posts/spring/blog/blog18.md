---
title: 18.[블로그 프로젝트] 인덱스페이지 글목록 표시하기
date: 2021-09-15 14:52:22
tags:
category:
    - Spring
    - Project
---
#### 화면 구성
일단 index.jsp를 수정하자.
```html
<%@ include file="layout/header.jsp"%>

<div class="container">
    <c:forEach var="board" items="${boards}">
        <div class="card m-2" >
            <div class="card-body">
                <h4 class="card-title">${board.title}</h4>
                <a href="#" class="btn btn-primary">상세보기</a>
            </div>
        </div>
    </div>
    </c:forEach>
</div>

<%@ include file="layout/footer.jsp"%>
```
JSTL을 이용해서 for each문을 사용했다.
이제 인덱스 분기 처리를 해야되는데, 이걸 하기위해 DB에서 내용들을 가져오는 일을 서비스에 구현해야 한다.

#### 글 조회를 서비스에 구현
```java
public class BoardService {
    @Autowired
    private BoardRepository boardRepository;
    public List<Board> postList() {
        return boardRepository.findAll();
    }
}
```
이제 분기처리를 하자

#### 컨트롤러에서 분기처리
```java
    @Autowired
    private BoardService boardService;

    @GetMapping({"/", ""})
    public String index(Model model){//스프링에서 데이터를 가져올 땐 Model이 필요!!
        model.addAttribute("boards", boardService.postList());
        return "index"; //그냥 컨트롤러는 return시 viewResolver가 작동! -> 해당 인덱스 페이지로 모델의 정보를 들고 이동!
    }
```
스프링에서 데이터를 가져올 땐 Model이 필요하다!
Model은 컨트롤러에서 생성한 데이터를 view로 전달할 때 사용한다.
model에 boards변수로 board리스트를 할당한다.