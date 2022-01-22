---
title: 12. 자바의 상속 내부 원리 (부모의 private 변수는 자식이 직접 접근 가능할까)
date: 2021-11-28 23:27:33
tags:
category:
    - Java
    - Java Basic
---

1. 부모의 private 멤버 변수는 자식이 직접 접근할 수 있을까?
   답은 **No**다.

2. 자식 객체가 생성되면, 부모 객체는 생성 될까?

   답은 **Yes**다

이를 검증하기 위해 코드를 짜보자.

## 예시코드

### 부모 클래스

```java
public class Parent {
	public Parent() {
		System.out.println("Parent!");
	}
	private String privateString = "private";
	protected String protectedString = "protected";
	String defaultString = "default";
	public String publicString = "public";
}
```

부모 객체가 생성되면, 부모 생성 문구를 출력하도록 했다.
그리고 접근 지시자 별 문자열을 멤버로 가지고 있다.

### 자식 클래스

```java
public class Sub extends Parent{
	public Sub() {
    //super(); 부모의 기본 생성자가 생략되어 있다.
		System.out.println("SUb class!");
	}
}
```

자식 객체는 생성자만 가지고 있다.

#### 이때 자식클래스는 부모의 private 멤버 변수와 생성자를 상속받지 않는다.

즉 privateString을 제외한 멤버변수를 자식 클래스도 직접 접근 가능하다.
자식 객체를 생성하면, 자식의 생성자가 실행된다. (**이때, super()에 의해 부모 생성자 먼저 실행된다!**)

그렇다고 자식 객체를 만들 때, 부모 객체와 자식 객체가 각각 생기는 게 아니다.
자식 객체를 만들어도 하나의 객체를 가진다.

다만 자식 객체가 생성 되기 전에 부모 객체가 생성되고, 부모 객체의 멤버가 초기화된다.
그 다음 자식 생성자가 실행되면서 부모 객체가 생성된 것에 자식 객체가 추가로 씌여진다.

쉽게말하면 (Object 객체 생성자 실행 -> 부모 객체 생성자 실행 -> 자식 객체 생성자 실행) 이런 식으로 실행되고,
이 세가지 클래스에 있는 멤버들은 한 객체(자식)에 초기화된다.

#### 그렇다면 다음 코드를 실행하면?

```java
public class Application {
	public static void main(String[] args) {
		Parent first = new Parent();
		Parent second = new Sub();
		Sub third = new Sub();
	}
}
```

다음과 같다!

```java
Parent!
Parent!
SUb class!
Parent!
SUb class!
```



> 참고 사이트

https://stackoverflow.com/questions/23093470/java-order-of-initialization-and-instantiation
