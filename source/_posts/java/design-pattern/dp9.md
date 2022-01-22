---
title: 9. 템플릿 메서드 패턴
date: 2021-11-27 21:14:40
tags:
category:
    - Java
    - Design Pattern
---

# 템플릿 메서드 패턴

템플릿 메서드 패턴은 특정 역할의 책임을 **한 메서드에 여러 메서드를 조합*(이게 템플릿!)*으로 정해놓는 패턴**이다.

**템플릿 메서드를 구성하는 메서드들은 역할에 속하는 객체마다 다르게 구현**될 수 있다.

## 활용 예시

우리는 여러 DB에 연결해야 한다.

우리가 연결한 DB는 모두 다음과 같은 절차를 따른다고 하자.
해당 DB드라이버 찾기 - DB에 로그인 - DB에 연결

이때 DB마다 사용하는 드라이버가 다르다.

이 상황에서 템플릿 메서드 패턴을 활용해서 문제를 해결해보자.

#### 템플릿 메서드를 포함한 추상 클래스

일단 추상 클래스 안에 주어진 템플릿을 구현한 메서드를 만들어보자!
이때 각 DB마다 달라지는 부분은 추상 메서드로 다형성을 확보한다.

```java
public abstract class DataConnection {
	//템플릿 메서드
  public final void process() {
		findDriver();
		login();
		connectDB();
	}

	public abstract void findDriver();
	public abstract void connectDB();

	public final void login() {
		System.out.println("로그인을 실행합니다...");
	}
}
```



#### 추상 클래스를 상속하는 자식 클래스

이제 DB마다 클래스를 상속해서 구현하자.
추상 메서드를 DB 요구 조건에 맞게 구현하면 된다.

*우리는 MySQL과 H2를 예시로 구현해보자*

```java
public class MySQLConnection extends DataConnection{
	@Override
	public void findDriver() {
		System.out.println("MySQL 드라이버를 찾습니다.");
	}

	@Override
	public void connectDB() {
		System.out.println("MySQL과 연결합니다.");
	}
}
```

```java
public class H2Connection extends DataConnection{
	@Override
	public void findDriver() {
		System.out.println("H2 드라이버를 찾습니다.");
	}

	@Override
	public void connectDB() {
		System.out.println("H2 드라이버를 연결합니다.");
	}
}
```



#### 결과 확인

이제 적용해보자.

```java
public class Application {
	public static void main(String[] args) {
		DataConnection dataConnection = new MySQLConnection();
		dataConnection.process();

		dataConnection = new H2Connection();
		dataConnection.process();
	}
}
```

```java
MySQL 드라이버를 찾습니다.
로그인을 실행합니다...
MySQL과 연결합니다.
H2 드라이버를 찾습니다.
로그인을 실행합니다...
H2 드라이버를 연결합니다.
```



