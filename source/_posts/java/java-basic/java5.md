---
title: 5. Class 클래스
date: 2021-08-17 15:10:14
tags:
category:
    - Java
    - Java Basic
---
## Class 클래스
자바의 모든 클래스, 인터페이스는 .class 파일로 저장됨
Class 클래스는 컴파일 된 class 파일을 로드하여 객체를 동적으로 로딩하고, 정보를 가져오는 메서드 제공


1. Class.forName("클래스 이름") 메서드로 클래스를 동적으로 로드
2. 클래스 이름으로 직접 가져오기
3. 인스턴스에서 가져오기
```java
//1.
Class c = Class.forName("java.lang.String");
//2.
Class c = String.class;
//3.
String s = new String();
Class c = s.getClass();
```
스트링 클래스의 정보를 c가 다룰 수 있게 된다.
이제 c의 인스턴스를 생성하고 싶으면 c.newInstace()로 생성할 수 있다.

### 동적 로딩
컴피일 시 데이터 타입이 정해지는 것이 아닌, 런타임 중에 정해지는 방법.
데이터 타입을 유연하게 사용할 수 있지만, 오류 발생 가능성이 높고 속도도 떨어짐
일반적으로 해당 자료형을 모를때 사용한다.
