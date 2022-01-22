---
title: 8. 객체지향 개념(상속, 업캐스팅, 오버라이딩, 인터페이스)
date: 2021-09-03 21:49:07
tags:
category:
    - Java
    - Java Basic
---
### 1. 상속
extends로 특정 객체의 필드와 메소드를 상속받을 수 있다.
이때, **super클래스의 private 필드나 메소드는 자식클래스도 직접 접근할 수 없다.**


> 그렇다면 자식 클래스는 상위 클래스의 멤버 변수를 어떤 원리로 사용할 수 있는 건가?

```java
public Super(){
    className = "super";
}
....
public Sub(){
    //super();를 컴파일러가 추가해준다.
    className = "Sub";
}
```
**즉 자식 클래스의 생성자에는 상위 클래스의 디폴트 생성자를 컴파일러가 추가한다.**


### 2. 업캐스팅 vs 다운캐스팅
**업캐스팅** : 서브 클래스 -> 슈퍼 클래스
서브 클래스 객체더라도 슈퍼 클래스의 필드, 객체만 사용가능.
```java
Super super = new Sub();
```


**다운 캐스팅** : 슈퍼 클래스 -> 서브 클래스
업캐스팅을 먼저 하고, 슈퍼 클래스를 괄호로.
```java
Sub sub = (Sub)super;
```


### 3. 오버라이딩 vs 오버로딩
**오버라이딩** : 같은 상속 관계 클래스 간의 같은 이름의 메서드를 **재정의**
```java
//super.java
public void methodExample(){
    ...
}
//sub.java
@Override
public void methodExample(){
    ...
}
```
이때 리턴형, 메소드 이름, 매개변수가 같아야한다!


**오버로딩** : 같은 이름 메소드를 매개변수 유형과 개수를 다르게 확장하는 행위
```java
//super.java
public void methodExample(){
    ...
}
public void methodExample(int i){
    ...
}
```


### 4. 클래스 vs 인터페이스
**클래스** : 
생성자 가짐
인스턴스화 가능
다중 상속 가능
여러 접근 제어자
A는 B의 추상 클래스 = B **is a kind of** A

**인터페이스** :
생성자 가질 수 없음
인스턴스화 불가능
다중 상속 가능
항상 public
A는 B의 인터페이스 = B **is able to** A

> 인터페이스 키워드 생략

```java
(public) interface 인터페이스이름 {
    (public static final) 변수타입 상수이름 = 값;
    (public abstract) 반환형 메서드이름(매개변수...);
}
```
괄호 안에 있는 키워드들은 우리가 생략해도, 컴파일러가 넣어준다.


> 인터페이스의 구현된 메소드 (default, static, private)

**static method**
구현된 메소드. 하지만 구현된 클래스에 상속되지 않는다! 
즉 인터페이스에서만 접근 가능한 메소드인 셈이다.


**default method**
이 친구도 구현된 메소드다. 근데 얘는 구현된 클래스에 상속된다.
디폴트 메소드는 인터페이스를 구현한 클래스들이 나중에 인터페이스에 추가된 메소드를 구현하지 않아도 사용가능하도록 하기 위해 추가됐다.


**private method**
이 녀석도 구현된 메소드이다.
static을 붙이기도 한다.
근데 이 녀석은 구현된 클래스에 상속되지 않는다.
이 녀석은 인터페이스 구현에 필요한 상세 내용을 캡슐할 때 사용된다.


> confilct
1. 여러 인터페이스의 디폴트 메서드 간의 충돌이 발생하면?
```java
interface a{
    default void method{}
}
interface b{
    default void method{}
}
public class c implements a, b{
    @Override
    public void method{}
}
```
오버라이딩으로 우리가 사용할 메소드를 재정의해야 컴파일 에러가 나지 않는다!


2. 디폴트 메서드와 상위 클래스의 메소드 간의 충돌이 난다면?
```java
class a{
    public void method{}
}
interface b{
    default void method{}
}
class c extends a implements b{}
```
이럴 경우 a의 method가 우선순위이므로, 수퍼 클래스의 메소드가 실행된다.


> Collections vs Collection

**Collection** :
콜렉션 프레임워크의 인터페이스
각 자료구조들이 공통적으로 가지는 메서드들을 포함
추상메소드, 정적메소드, 디폴트메소드 모두를 포함가능


**Collections**
콜렉션 프레임워크 객체들을 유용하게 사용할 수 있는 유틸리티 메소드가 정의된 클래스
모든 메소드가 정적메소드.
