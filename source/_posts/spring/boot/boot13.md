---
title: REDIRECT와 FORWARD의 차이!!
date: 2021-11-29 15:09:24
tags:
category:
  - Spring
  - Boot
---

# REDIRECT와 FORWARD의 차이

## REDIRECT

리다이렉트는 "서버가 클라이언트에게 해당 URL로 다시 요청하라고 명령하는 것"이다.

1. 클라이언트가 서버의 URL에 **요청** (get이던 post던 상관없이)
2. 서버의 컨트롤러가 해당 URL에 매핑된 메서드 실행
3. 해당 메서드가 다른 URL로 리다이렉트 시킬 경우, **클라이언트는 그 URL로 GET 요청을 새로 실행!**

### REDIRECT의 특징

리다이렉트는 클라이언트에게 **새로운 GET 요청을 하도록 명령하는 것**이다.

따라서 **새로운 요청**이 만들어져서, 스프링의 **모델 같은 값이 모두 초기화 된다!!**

대신 GET 요청을 새롭게 실행한 것이라서 브라우저의 **GET 요청한 URL로 달라지고**,
**새로고침할 경우 GET 요청이 반복되서 실행되는 것**이므로,
반복된 POST를 방지 할 수 있다.(이건 포워드에서 더 잘 알아보자.)

### REDIRECT하는 컨트롤러 예시

```java
...
  	@GetMapping("/redirect")
	public String getRedirect() {
		return "redirect:/result";
	}

	@PostMapping("/redirect")
	public String postRedirect() {
		list.add(++i);
		return "redirect:/result";
	}

	//리다이렉트하면 다음 메서드로 get요청이 간다
	@GetMapping("/result")
	@ResponseBody
	public String getResult() {
		return "get method" +getListString();
	}
...
```



## FORWARD

포워드는 "서버가 포워딩한 URL이 매핑된 메서드를 실행하고 그 결과값을 클라이언트에게 보여준다."

즉 서버가 클라이언트에게 해당 URL을 GET 요청으로 다시 하라고 하는 것이 아닌,
자기가 해당 URL 매핑된 메서드를 찾아서 실행한다!!

이때 중요한 건 처음 클라이언트가 보낸 요청 그대로 유지한다는 점이다.
리다이렉트는 처음 클라이언트가 보낸 요청은 종료하고, 보내고 싶은 URL로 새로운 GET 요청을 통해 접근했지만,
포워드는 처음 클라이언트가 보낸 **요청을 끝내지 않고 서버가 보내고 싶은 URL로 처음 요청을 유지한 상태로 접근한다.**

### FORWARD의 특징

포워드는 새로운 요청을 생성하지 않는다.
다만 **다른 URL에 매핑된 메서드에 접근**할 뿐이다.

그래서 POST요청이 온 상황에서 포워드하면,
포워드한 URL로 POST method로 매핑된 메서드에 접근하려고 한다.
**즉 기존 요청의 HTTP method를 유지한 상황에서 접근한다**.

기존의 요청이 보존된 상황이므로, 스프링의 **모델 같은 값이 다른 URL로 포워딩되도 유지된다**.

이때 중요한 건 클라이언트 요청 처리 과정에 포워드가 포함된다는 사실이다.
(리다이렉트는 클라이언트의 처음 요청을 종료시키고 새로운 GET 요청을 만들었다. 즉 분리된다.)
그래서 **포워딩하고 나서도 클라이언트의 URL은 처음 요청했던 URL과 동일**하다.

그 상태로 **새로고침을 하면, 맨 처음 클라이언트의 요청을 다시 시작하는 것**이다.
즉 POST를 클라이언트가 요청하고, 서버가 view 관련 URL로 포워딩 시킨다고 했을 때,

클라이언트는 view를 볼 수 있지만, URL은 클라이언트가 POST 요청하던 그대로이다.
이 상태에서 새로고침을 하면 다시 클라이언트의 POST 요청 -> 서버가 veiw 관련 URL 포워딩 과정을 다시하게 된다.

즉 **POST를 반복해서 하게 된다!!!!**



### FORWARD하는 컨트롤러 예시

```java
...
  @GetMapping("/forward")
	public String getForward() {
		return "forward:/result";
	}

	@PostMapping("/forward")
	public String postForward() {
		list.add(++i);
		return "forward:/result";
	}

//이전 요청에 따라 매핑된다.
	@GetMapping("/result")
	@ResponseBody
	public String getResult() {
		return "get method" +getListString();
	}

	@PostMapping("/result")
	@ResponseBody
	public String postResult() {
		return "post method" +getListString();
	}
...
```



## 직접 테스트 해보기

### 컨트롤러 클래스

```java
@Controller
public class RedirectController {

	List<Integer> list = new ArrayList<>();
	int i = 0;

	public String getListString() {
		StringBuilder sb = new StringBuilder();
		list.stream().forEach(integer -> sb.append(integer));
		return sb.toString();
	}

	@GetMapping("/result")
	@ResponseBody
	public String getResult() {
		return "get method" +getListString();
	}

	@PostMapping("/result")
	@ResponseBody
	public String postResult() {
		return "post method" +getListString();
	}

	@GetMapping("/test")
	public String test() {
		return "test";
	}

	@GetMapping("/redirect")
	public String getRedirect() {
		return "redirect:/result";
	}

	@PostMapping("/redirect")
	public String postRedirect() {
		list.add(++i);
		return "redirect:/result";
	}

	@GetMapping("/forward")
	public String getForward() {
		return "forward:/result";
	}

	@PostMapping("/forward")
	public String postForward() {
		list.add(++i);
		return "forward:/result";
	}
}
```



### JSP 화면

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<form method="post" action="/redirect">
    <input type="submit" value="리다이렉트">
</form>

<form method="post" action="/forward">
    <input type="submit" value="포워드">
</form>
</body>
</html>
```
