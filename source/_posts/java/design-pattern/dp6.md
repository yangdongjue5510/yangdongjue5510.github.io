---
title: 6. 싱글톤 패턴
date: 2021-10-01 13:21:35
tags:
category:
    - Java
    - Design Pattern
---
## 싱글톤 패턴
인스턴스가 하나 뿐인 객체를 만드는 패턴!
어디서든 그 인스턴스에 접근할 수 있게하는 패턴!

### 왜 쓰는가?
굳이 여러개가 필요 없는 클래스의 객체가 여러개 생기면 메모리를 불필요하게 차지하게 된다.
이런 클래스들의 객체를 하나로 유지하도록 설계해서 메모리를 효율적으로 사용하게 한다.

혹은 설정이나 보안같이 중요한 내용을 담는 클래스는 여러개의 객체를 만들면 안정성을 해친다.


- 전역변수 쓰면 되잖아요
    - 전역변수는 시작하면서 생성(JVM마다 다름). 필요하지 않을 때 생성될 수 있음.

### 고전적인 싱글톤 패턴
```java
public class Singleton {
    private static Singleton uniqueObject;
    private Singleton(){}
    public static Singleton getInstance(){
        if(uniqueObject == null){
            uniqueObject =new Singleton();
        }
        return uniqueObject;
    }
}
```
1. 유일한 객체는 private static으로 정적으로 만들자.
2. 객체에 대한 접근은 public static한 메소드로 하자.
    - 메소드가 현 객체의 유무를 파악해서 객체를 만들어 반환.


> 멀티 스레드 문제

만약 여러개의 스레드가 getInstance메소드에 접근하면 어떻게 될까?

(1번 스레드와 2번 스레드가 getInstace를 호출한 상황...)
1번 getInstance -> 2번 getInstance -> 
1번 객체 유무 판별(없다고 판단) -> 2번 객체 유무 판별(없다고 판단) ->
1번 객체 생성 -> 2번 객체 생성(문제 발생!!!!!!) ->
1번 객체 반환(1번 객체 반환) -> 2번 객체 반환(2번 객체 반환)

즉 두 스레드가 반환한 객체는 서로 다른 객체다!!!!


### 스레드 문제 해결하기
1. **getInstance를 synchronized하기.**
    - getInstance 호출 시 끝날 때까지 다른 스레드 정지.
    - 하지만 매번 객체에 접근할 때마다 동기화하는건 속도 문제 발생 가능
    - getInstance의 속도가 중요하지 않은 경우 사용.


2. **인스턴스를 필요할 때 만들지 말고 처음부터 만들기**
```java
public class Singleton {
    private static Singleton uniqueInstance = new Singleton();

    private Singleton();
    public static Singleton getInstance(){return uniqueInstance;}
}
```
이 방법은 JVM에서 클래스가 로딩될 때 인스턴스를 생성!


3. **게으른 홀더 방식**
내부 클래스를 따로 선언해서 그 안에 객체를 저장하는 방식이다.
클래스 로더가 이 클래스를 읽을 때 객체 생성된다.
```java
public class Singleton2 {
    private Singleton2(){}
    public static class LazyHolder{
       private static final Singleton2 uniqueInstance = new Singleton2();
    }
    public static Singleton2 getInstance(){return LazyHolder.uniqueInstance;}
}
```
synchronized를 사용하지 않아 성능도 좋고, 다른 키워드를 사용하지 않아도 되서 직관적이다.

> 주의 할 점

만약 여러개의 클래스 로더를 사용할 경우 여러개의 객체가 생성될 수 있다!
구체 클래스에 의존하여 인스턴스를 찾게 되므로, DIP, OCP를 위반할 가능성이 있다.