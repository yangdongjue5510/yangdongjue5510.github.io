---
title: 19.[블로그 프로젝트] 인덱스 글목록 페이징
date: 2021-09-17 13:40:45
tags:
category:
    - Spring
    - Project
---
서비스 구현 - 컨트롤러 연결 - jsp 수정

### BoardService 수정하기
```java
import java.util.List;
//스프링이 컴포넌트 스캔을 통해서 Bean에 자동 등록. IoC를 해줌
@Service
public class BoardService {
    @Autowired
    private BoardRepository boardRepository;
    
    @Transactional(readOnly = true)
    public Page<Board> postList(Pageable pageable) {
        return boardRepository.findAll(pageable);
    } //찾아서 페이지에 맞도록 분류하고 페이지 객체로 반환
}
```
페이지 설정을 담은 객체인 Pageable을 인수로 받아, Pageable에 맞도록 데이터를 분류한 Page객체를 반환.

### BoardController 수정하기
```java
@Controller
public class BoardController {
    @Autowired
    private BoardService boardService;

    @GetMapping({"/", ""})
    public String index(Model model, @PageableDefault(size=3, sort = "id", direction = Sort.Direction.DESC)Pageable pageable){
        model.addAttribute("boards", boardService.postList(pageable));
        return "index";
    }

}
```
스프링에서 html로 데이터를 가져올 때는 Model이 필요하다.
그리고, Pageable에 페이지 설정을 저장해서 인수로 전달한다.

model에 서비스에서 수정한 postList를 boards와 함께 속성을 추가한다.
그리고 index를 반환해서 뷰리졸버가 index.jsp를 찾아 출력하도록한다.

### index.jsp 수정
```html
<%@ include file="layout/header.jsp"%>

<div class="container">
    <c:forEach var="board" items="${boards.content}">
        <div class="card m-2" >
            <div class="card-body">
                <h4 class="card-title">${board.title}</h4>
                <a href="#" class="btn btn-primary">상세보기</a>
            </div>
        </div>
    </c:forEach>
    <ul class="pagination justify-content-center">
<%--        플렉스 성질을 가진 객체를 가운대로 모으기.--%>

<%--        버튼 누를때마다 페이지 이동 --%>
    <c:choose>
        <c:when test = "${boards.first}">
            <li class="page-item disabled"><a class="page-link" href="?page=${boards.number-1}">Previous</a></li>
        </c:when>
        <c:otherwise>
            <li class="page-item"><a class="page-link" href="?page=${boards.number-1}">Previous</a></li>
        </c:otherwise>
    </c:choose>
    <c:choose>
        <c:when test = "${boards.last}">
            <li class="page-item disabled"><a class="page-link" href="?page=${boards.number+1}">Next</a></li>
        </c:when>
        <c:otherwise>
            <li class="page-item"><a class="page-link" href="?page=${boards.number+1}">Next</a></li>
        </c:otherwise>
    </c:choose>
    </ul>
</div>
<%@ include file="layout/footer.jsp"%>
```
page객체는 해당 페이지가 처음인지를 알리는 first, 마지막을 알리는 last, 내용인 content가 있다.
첫 페이지면, previous 버튼을 비활성화하고 마지막 페이지는 next 버튼을 비활성화 한다.
