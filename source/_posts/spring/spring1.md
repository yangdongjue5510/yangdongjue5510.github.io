---
title: 스프링 프레임워크의 기본 개념 정리 (POJO, Ioc/DI, AOP)
date: 2021-11-15 11:44:25
tags:
category:
    - Spring
---

# 스프링 프레임워크의 기본 개념 정리 (POJO, Ioc/DI, AOP)

## POJO

포조는 plane old java object란 의미인데, 필자도 처음에 이게 뭔 소린지 잘 이해가 안갔다.
쉽게 말하면, 기본 자바의 기능 외에 다른 클래스를 사용하지 말자는 뜻이다.
클래스나 메소드 시그니처를 개발자가 필요한 대로 만들어서 사용하자는 의미다.

서블릿으로 자바 웹 애플리케이션을 만들면, 서블릿의 구조를 따라서 만들어야 한다.
예를 들면 이런 식이다.

### POJO가 아닌 경우

```java
public class DeleteBoardServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

  @Override
	protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		String seq = request.getParameter("seq");
		

		BoardVO vo = new BoardVO();
		vo.setSeq(Integer.parseInt(seq));
		
		BoardDAO boardDAO = new BoardDAO();
		boardDAO.deleteBoard(vo);

		RequestDispatcher dispatcher = request.getRequestDispatcher("getBoardList.do");
		dispatcher.forward(request, response);
	}
}
```

자세히 보면 HttpServlet이라는 클래스를 상속하고,
service 메소드를 오버라이딩해서 그 안에 비즈니스 로직을 작성하고 있다.

이때 비즈니스 로직에는 response 객체가 사용되지 않는다.(포워딩할 때만 사용된다.)
그럼에도 불구하고 service 메소드의 고정적인 구조로 인해 response를 인수로 가져와야 한다.

POJO는 이런 불필요한 구조를 탈피해서, 
개발자가 본인에게 필요한 비즈니스 로직을 구현할 때 필요한 만큼의 기본 자바 객체만 사용하도록 한다.

POJO를 지키면 이런 식이 될 것이다.

### POJO인 경우

```java
@Controller
public class Cont{
  
  @RequestMapping("/hello/{seq}")
  public String helloController(@PathVariable int seq){
    BoardVO vo = new BoardVO();
		vo.setSeq(seq);
		
		BoardDAO boardDAO = new BoardDAO();
		boardDAO.deleteBoard(vo);
    return "getBoardList.do";
  }
}
```



## IoC/DI 제어의 역전/의존성 주입

### Ioc 제어의 역전

제어의 역전은 그냥 쉽게 말하면 이제 객체를 스프링 컨테이너라는 녀석이 한다는 의미다.(컨테이너는 나중에 더 알아보자.)

```java
@Controller
public class UserController {

    @Autowired
    private UserService userService;
    @Autowired
    private AuthenticationManager authenticationManager;

    @GetMapping("/auth/joinForm")
    public String joinForm(){
        return "/user/joinForm";
    }

    @GetMapping("/auth/loginForm")
    public String loginForm(){
        return "/user/loginForm";
    }
  
  ....
    
}
```

스프링으로 작성된 컨트롤러의 일부 예시다.
보면 @Autowired라는 어노테이션을 볼 수 있다.

이는 해당 변수에 스프링 컨테이너가 관리하고 있는 객체 중 적절한 것을 할당해달라는 의미이다.
(좀 어려운 말로는 의존성을 주입한다고도 말한다.)

개발자가 일일히 객체를 싱글톤으로 만들어서 그 싱글톤 객체를 직접 불러와서 변수에 할당할 필요 없다는 의미다.
스프링 컨테이너가 알아서 객체를 싱글톤으로 메모리에 로딩시켜놓고, 개발자가 요청하면 스프링 컨테이너가 알아서 할당해줄 것이다.



### DI 의존성 주입

일단 이 어려운 용어를 이해하기 전에 의존성이 뭔지 좀 알아보자.

검색을 해보면 "A가 동작하기 위해서 B가 반드시 필요한 경우, A는 B에 의존한다." 라는 의미인데,
우리가 바로 위에 썼던 예시를 보면, UserService, AuthenticalManager라는 객체 두 개가 UserController 객체에 사용된다.

즉 UserController 객체는 이 두 객체에 의존한다는 의미다.

그런데 의존하려면, 두 객체가 필요하다.
예시에서는 참조변수만 쓰여있다.

만약 객체를 가져오려면, 직접 new해서 객체를 만들거나,
메모리 상에 있는 해당 객체를 직접 가져와야 할 텐데,

스프링에서는 스프링 컨테이너가 메모리 상에 있는 객체를 가져와서,
해당 레퍼런스 변수에 할당해, 의존 관계를 만들어 준다는 의미다.

즉, 클래스 외부에서 적절한 객체를 찾아와서 해당 클래스와 의존성을 갖도록 주입한다는 의미다!!



> 스프링에서 의존성 주입하는 여러가지 방법

1. **어노테이션 활용하기 @Autowired**

   레퍼런스 변수 위에 @Autowired 어노테이션을 붙이면 알아서 의존성이 주입된다.
   다만, 주입을 하기 전에 컴포넌트 스캔을 해야 한다.

   ```xml
   <!-- 해당 패키지 하위에 있는 모든 어노테이션을 검사 -->
   <context:component-scan base-package="com.fastcampus.biz"/>
   ```

   

2. **xml 태그 활용하기**

   다음과 같이 xml에 빈등록 되어 있다고 하자.(web.xml이나 컨텍스트 xml)

   ```xml
   <bean 
     id="indexService" 
     class="com.baeldung.di.spring.IndexService" />
   ```

   IndexService라는 클래스를 스프링이 관리하는 객체(빈)로 등록했다.
   이제 이 객체를 IndexApp이라는 클래스에서 사용하고자 한다고 가정해보자.

   

   **a. 생성자로 의존성 주입하기**

   ```xml
   <bean 
     id="indexApp" 
     class="com.baeldung.di.spring.IndexApp">
       <constructor-arg ref="indexService" />
   </bean>  
   ```

   생성자로 레퍼런스 변수를 초기화 할 수 있다.
   IndexApp이 빈으로 등록 될 때, 설정한 대로 객체를 주입시켜서 객체 생성될 것이다.

   

   **b. 프로퍼티로 의존성 주입하기**

   ```xml
   <bean 
     id="indexApp" 
     class="com.baeldung.di.spring.IndexApp" >
       <property name="service" ref="indexService" />
   </bean>
   ```

   프로퍼티 태그로 해당 레퍼런스 변수에 의존성 주입할 수 있다.
   다만 setter메소드가 정의되어 있어야 한다.



## AOP 관점 지향 프로그래밍

AOP를 이해하기 위해서는 일단 관점(Aspect)를 이해해야 한다.

요청을 처리할 때, 핵심 비즈니스 로직외에도 필요한 여러가지 공통된 처리가 있다.
사용자가 권한이 있는지... 요청이 제대로 된 경우 사후처리 등...

이제 비즈니스 로직에 해당하는 메서드와 이런 공통된 처리들을 따로 분리해서 관리하자는게 시작이다.(관심 분리)
그래야 개발자가 비즈니스 로직에 집중할 수 있고, 중복된 코드 작성도 줄어들 것이다.

개발자가 호출한 모든 핵심 비즈니스 로직 메서드를 **조인 포인트** 라고 한다.

조인 포인트 중 개발자가 필터링한 비즈니스 로직 메서드를 **포인트 컷**이라고 한다.

일괄적으로 적용해줘야하는 공통 처리는 **어드바이스**라고 한다.
(어드바이스는 적용시기나 종류에 따라 다섯가지로 나뉜다.)



모듈들의 공통 처리를 담당하는 **어드바이스** 와  특정 모듈의 핵심 비즈니스 로직인 **포인트컷** 을 결합(위빙)한 것을 **Aspect** 라고 한다.



### AOP 구현 하기

AOP 구현도 어노테이션 방법과 xml 태그 방법 두가지로 구현 가능하다.

1. **xml 태그로 AOP 구현**

   일단 어드바이스가 구현된 클래스를 먼저 구현하자(어라운드어드바이스를 예시이다.)

   ```java
   public class AroundAdvice { 
   	public Object aroundLog(ProceedingJoinPoint jp) throws Throwable {
   		// JoinPoint를 상속한 ProceedingJoinPoint를 이용하면 클라이언트가 호출한 비즈니스 메소드 정보를 알 수 있다.
   		String method = jp.getSignature().getName();
   		Object returnObj = null;
   
   		StopWatch watch = new StopWatch();
   		watch.start();
   		// 실제 클라이언트가 호출한 비즈니스 메소드가 실행되는 시점
   		returnObj = jp.proceed(); 
   		watch.stop();
   		System.out.println(method + "() 메소드 수행에 소요된 시간 : " + watch.getTotalTimeSeconds() + "(초)");
   		
   		return returnObj;
   	}
   }
   ```

   이때 주목할 점은 ProceedingJoinPoint jp를 매개변수로 받는다는 점이다.
   (이는 Around 어드바이스만의 특징이다. 다른 종류의 어드바이스는 JointPoint 객체를 매개변수로 받는다.)

   

   그리고 어드바이스를 빈 등록한다.

   ```xml
    	<!-- 횡단관심(어드바이스)에 해당하는 클래스를 bean 등록한다 -->
    	<bean id="AroundAdvice"    class="com.fastcampus.biz.common.BeforeAdvice"/>
    	<bean id="afterThrowing"  class="com.fastcampus.biz.common.AfterThrowingAdvice"></bean>
   ```

   

   그리고 AOP 설정을 해준다.

   ```xml
    	<aop:config>
    	 	<aop:pointcut id="allPointcut" expression="execution(* com.fastcampus.biz..*Impl.*(..))"/>
    	 	
    	 	<aop:aspect ref="beforeAfter">
    	 		<aop:before pointcut-ref="allPointcut" method="beforeLog"/>
    	 		<aop:after-returning pointcut-ref="getPointcut" method="afterLog" returning="returnObj"/>
    	 	</aop:aspect>
    	 	
    	 	<aop:aspect ref="afterThrowing">
    	 		<aop:after-throwing pointcut-ref="allPointcut" method="exceptionLog" throwing="exceptionObj"/>
    	 	</aop:aspect>
    	 	
    	 	<aop:aspect ref="around">
    	 		<aop:around pointcut-ref="allPointcut" method="aroundLog"/> 	 	
    	 	</aop:aspect>
    	</aop:config>
   ```

   이때 주목할 점은, 포인트컷을 정의하고, 애스펙트를 어드바이스와 포인트컷을 연결해준다.

    

2. **어노테이션으로 AOP 구현하기**

   일단 어노테이션으로 하려면 다음 태그를 추가해야한다.

   ```xml
   <aop:aspectj-autoproxy/>
   ```

   

   이제 포인트컷을 설정해보자(@Aspect를 붙여주자.)
   어노테이션 기반에서는 클래스를 만들어서 포인트컷을 설정할 수 있다.

   ```java
   @Aspect
   public class BoardPointcut {
   	@Pointcut("execution(* com.fastcampus.biz..*Impl.*(..))")
   	public void allPointcut() {}
   	
   	@Pointcut("execution(* com.fastcampus.biz..*Impl.get*(..))")
   	public void getPointcut() {}
   	
   	@Pointcut("execution(* com.fastcampus.biz.board.*Impl.*(..))")
   	public void boardPointcut() {}
   	
   	@Pointcut("execution(* com.fastcampus.biz.user.*Impl.*(..))")
   	public void userPointcut() {}
   }
   ```

   

   그러면 어드바이스를 다음과 같이 작성할 수 있다.
   @Service로 빈등록을 처리하고, @Aspect로 애스팩트 등록을 할 수 있다.
   자세히보면, 어노테이션으로 어드바이스 종류를 선언하고 포인트컷을 매개변수로 넣어준다.

   ```java
   @Service
   @Aspect // Aspect = Pointcut(핵심 관심) + Advice(횡단 관심)
   public class AroundAdvice { 
   
   	@Around("BoardPointcut.allPointcut()")
   	public Object aroundLog(ProceedingJoinPoint jp) throws Throwable {
   		// JoinPoint를 상속한 ProceedingJoinPoint를 이용하면 클라이언트가 호출한 비즈니스 메소드 정보를 알 수 있다.
   		String method = jp.getSignature().getName();
   
   		Object returnObj = null;
   
   		StopWatch watch = new StopWatch();
   		watch.start();
   		// 실제 클라이언트가 호출한 비즈니스 메소드가 실행되는 시점
   		returnObj = jp.proceed(); 
   		watch.stop();
   		System.out.println(method + "() 메소드 수행에 소요된 시간 : " + watch.getTotalTimeSeconds() + "(초)");
   		
   		return returnObj;
   	}
   }
   ```

   
