---
title: 7. 객체지향 입문(생성자, this, static)
date: 2021-09-01 14:25:42
tags:
category:
    - Java
    - Java Basic
---
### 1. 클래스 vs 객체 vs 인스턴스
```java
public class CreditCard{
    ...
    public static void main(String[] args){
        CreditCard card1, card2;//객체
        card1 = new CreditCard();//인스턴스
        card2 = new CreditCard();//인스턴스
    }
}
```
클래스는 class 키워드 뒤에 붙여진 CreditCard
객체는 클래스의 타입으로 생성된 실체.
인스턴스는 메모리에 할당된 객체.

### 2. 생성자
객체에 구현되어 있는 생성자.
디폴트 생성자(매개변수가 없는 생성자)는 생략되어 추가되어 있음.


> 생성자는 참조 데이터 타입에만 허용. 기본 데이터 타입은 객체가 아니므로 불필요.

### 3. 접근 제어자
private : 같은 클래스 내부에서만 접근!
default : 같은 패키지에서만 접근!
protected : default + 상속 관계 클래스 접근가능!
public : 어디서나 접근 가능~!

### 4. this
1. 생성된 인스턴스의 메모리 주소
2. 같은 클래스 내의 생성자. this(~~)이런식을 사용
3. 자신의 주소

### 5. static
여러 인스턴스가 공유하는 메소드, 변수, 클래스에 사용하는 키워드
프로그램 실행 시 Method area에 생성되고, 프로그램 실행 종료 후 사라진다.


이때 static 메서드(클래스 메서드)는 인스턴스 변수를 사용할 수 없다!!!!(인스턴스 변수가 메모리에 아직 로딩되지 않았으므로.)


그리고 정적 메소드는 오버라이드 하면 안된다!

> 깊은 복사 vs 얕은 복사

얕은 복사 : 객체의 참조값을 복사!->원본이 변경되면 복사본도 변경!
깊은 복사 : 객체의 실제 값을 복사->값이 변경되도 복사본은 상관 없음.
System.arraycopy() : 얕은 복사
.clone() : 깊은 복사 (원소 별로 new 인스턴스화)



객체는 
char[]기반 가변 문자열!
AbstractStringBuilder 이런 걸 상속받아 만든다!!
StringBuffer = 동기화 o (synchronized)
StringBuilder = 동기화 x
생성시 16
trimToSize() = 실제 문자열만큼 용량을 줄임.

리플렉션 프로그래밍
JVM에서 런타임동작을 감시하거나 수정할 수 있는 기능이 있는 프로그램 (스프링 프레임워크, 하이버네이트)

예를들면, 클래스 파일이 없어도, Class 클래스를 활용...

static은 왜 오버라이딩 안되냐?
