---
title: REST Api 프로젝트에서 만난 소소한 문제들
date: 2021-12-02 11:44:58
tags: 
    - reshapi project
    - troubleShooting
category:
    - Spring
    - Project
---

Rest api 프로젝트를 하면서 배운 것들을 정리하고자 한다.

## JPA entity에서 배운 것들

```java
@Entity
@Getter @Setter
public class Investments {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(nullable = false)
	private Long investId;

	@Column(nullable = false)
	private Long userId;

	@ManyToOne	//다대일 관계
	@JoinColumn(name="id")
	private Products product;

	@Column(nullable = false)
	private Long investmentAmount;

	@Temporal(TemporalType.DATE)
	private Date investmentDate = new Date();	 //기본값을 현재로??

	@Enumerated(EnumType.STRING)
	private InvestmentStatus investmentStatus = InvestmentStatus.VALID;		//기본값을 유효로 ?
}
```

JPA와 디비에는 사용하고 싶지 않지만, VO 클래스 멤버 변수로 사용하고 싶은 변수는 **@Transient**를 붙인다.

이번 프로젝트에서는 h2 디비를 썼다. h2에서는 기본키 자동증가기능을 사용하려면 **@GeneratedValue(strategy = GenerationType.IDENTITY)** 를 써야 잘 작동했다. 

칼럼의 NOT NULL은 **@Column(nullable = false)** 로 처리했다. (@NotNull은 안됐다.)

**@Temporal(TemporalType.DATE)** 는 날짜시간 데이터 중 날짜 데이터를 저장한다는 의미다.
시분초는 0으로 처리되어 저장된다.

**@Enumerated(EnumType.STRING)**은 열거형 데이터를 저장할 때 쓰인다.
이번 프로젝트는 열거형 이름 그 자체를 도입했다.



## 헤더 값 읽기

```java
	@GetMapping("/product")
	public ResponseDto<List<Products>> productGet(@RequestHeader("X-USER-ID") long userId) {
		...
	}
```

@RequestHeader안에 헤더의 키값을 넣어주면 가져올 수 있다!



## SpringBoot Could not find acceptable representation(Error: Request failed with status code 406)

```java
@Getter @Setter
public class ResponseDto<T> {
	public ResponseDto(T data, HttpStatus status) {
		this.status = status;
		this.data = data;
	}

	HttpStatus status;
	T data;
}
```

406에러는 return된 객체에 setter getter가 없어서 일어났었다.



## stream으로 합구하기!

```java
List<Long> numbers = Arrays.asList(1L, 2L, 3L, 4L, 5L);

// Stream의 reduce 이용
Long sum1 = numbers.stream().reduce(0L, Long::sum);
```

reduce를 이용하면 구할 수 있었다!!



## JSON으로 통신할 때 Object Mapper로 케이스 변환하기

```java
@JsonNaming(value = PropertyNamingStrategy.SnakeCaseStrategy.class)
public class User {
  private phoneNumber; //json에는 phone_number로 보임
  
  @JsonProperty("user_name") //특정 변수에만 적용할 경우.
  private userName
}
```



## JSON을 반환할 때 ResponseEntity

나는 커스텀해서 ResponseDto를 만들었는데 자바에서는 이미 ResponseEntity가 있더라 크흠...

## SQL INIT

어플리케이션을 시작할 때 SQL을 실행해서 스키마를 만들고 데이터를 입력할 수 있다.

```yml
spring:
  sql:
    init:
      platform: h2
      mode: always
```

**application.yml**에 다음같이 적으면, 항상 어플리케이션을 시작할 때 SQL을 초기화해준다.

Resources 폴더에 schema-플랫폼이름.sql , data-플랫폼이름.sql 을 실행한다.

위 예시는 플랫폼을 h2로 했으니, **schema-h2.sql**, **data-h2.sql**이라고 하면된다!!

이때 **ddl-auto**는 꺼줘여야 잘 작동하게 된다!!

## JPA Method이름으로 쿼리 만들기

```java
public interface InvestmentsRepository extends JpaRepository<Investments, Long> {

	default Investments updateInvestStatus(Investments invest, InvestmentStatus status) {
		invest.setStatus(status);
		save(invest);
		return invest;
	}

	List<Investments> findInvestmentsByIdAndStatusEquals(long productId, InvestmentStatus investmentStatus);

	List<Investments> findInvestmentsByUserId(long userId);

	Investments findInvestmentsByUserIdAndIdAndStatusEquals(long UserId, long productId, InvestmentStatus status);
}
```

레퍼지토리에 우리가 원하는 쿼리의 역할을 메서드 이름으로 만들면 그에 해당하는 쿼리를 실행하는 메서드가 만들어진다.



## 전역 예외 핸들러

```java
@ControllerAdvice
@RestController
public class GlobalExceptionHandler {
	@ExceptionHandler(Exception.class)
	public ResponseDto<String> handleException(Exception e) {
		return new ResponseDto<>(e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
	}
}
```

예외 핸들러를 따로 만들면, 모든 예외 발생을 다음 메서드로 처리한다.

## 생성자 주입

```java
@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class ApiController {

	private final ProductsService productsService;

	private final InvestmentsService investmentsService;
  
  ...
}  
```

@Autowired 키워드를 쓰는 것보다 @RequiredArgsConstructor와 private final 키워드를 활용한 생성자 주입이 스프링에서 더 권장하는 방법이다.

나도 이 방법을 사용하기 전까지 순환 참조를 하고 있는 줄 모르고 있었는데, 생성자 주입으로 변경하는 과정에서 실수를 확인 할 수 있었다.
