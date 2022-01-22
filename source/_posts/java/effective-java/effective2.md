---
title: 생성자에 매개변수가 많으면 빌더를 고려하자
date: 2021-10-16 22:08:41
tags:
  - Effective Java
category:
  - Java
---



[점층적 생성자 패턴](#점층적-생성자-패턴)
[자바빈즈 패턴](자바빈즈-패턴)
[빌더 패턴](빌더-패턴)

### 점층적 생성자 패턴

클래스에 멤버 변수가 많다면, 수많은 생성자(점층적 생성자 패턴)가 가능하다.

```java
class Clazz{
  int a;
  double b;
  String c;
  ....
  public Clazz(int a){}
  public Clazz(int a, double b){}
  public Clazz(int a, double b, String c){}
  ...
}
```

근데 이런 방식은   **굳이 초기화 하고 싶지 않은 변수도 입력**해줘야 된다.
입력해야 될 매개변수가 적으면 나쁘지 않지만, **매개변수가 많아지면 코드 가독성을 해친다.**



### 자바빈즈 패턴

매개변수가 없는 생성자로 객체를 만들고, **세터 메서드를 호출**해 원하는 매개변수 값을 설정하는 방식.

```java
class Clazz{
  private int a; 
  private double b; 
  private String c;
  
  public void setA(int a){this.a = a;}
  public void setB(double b){this.b = b;}
  public void setC(String c){this.c = c;}
}
```

> 단점

이 방식은 한 객체를 만들 때 **메소드를 여러개 사용해야** 되고, 완전히 초기화가 되기 전엔 일관성이 무너지게 된다.
또한 **클래스를 불변으로 만들 수 없다.** (만들고 나서 인스턴스 멤버에 접근해서 수정할 수 있어야 하므로..)

```java
Clazz clazz = new Clazz();
clazz.setA(1);
clazz.setB(3.5);
clazz.setC("hi.");
//이제 객체 초기화 끝...!
```



### 빌더 패턴

점층적 생성자 패턴과 자바빈즈 패턴의 장점만 취한 방식이다.

- 원하는 클래스 안에 정적 클래시로 빌더를 만들고, 
- 빌더 기본 생성자(혹은 정적 팩토리 메서드)에 필수 매개변수를 받고,
- 나머지 선택 매개변수들은 빌더가 제공하는 세터 메서드로 초기화 해준다.
- 원하는 멤버들을 모두 초기화했을 경우, 불변객체를 반환하는 build 메서드로 완성한다.

```java
public class Employee{
	//가능한 모든 멤버 변수들.
  private final int age;
  private final int wage;
  private final String name;
  private final String company;
  private final String role;
  
  public Employee(Builder builder){
    age = builder.age;
    wage = builder.wage;
    name = builder.name;
    company = builder.company;
    role = builder.role;
  }
  public static class Builder {
    //멤버 변수 중 필수로 들어가야 할 변수.
    private final String name;
    private final String role;
    
    //선택 매개변수 기본값으로 초기화 한다.
    private int age = 0;
    private int wage = 0;
    private String company ="";
    
    //빌더의 기본 생성자. 필수 매개변수를 받아야 객체 생성이 가능하다는 의미다.
    public Builder(String name, String role){
      this.name = name;
      this.role = role;
    }
    
    //선택 매개변수 setter 메서드
    public Builder age(int age){
      this.age = age;
      return this;
    }
    
    public Builder wage(int wage){
      this.wage = wage;
      return this;
    }
    
    public Builder company(String company){
      this.company = company;
      return this;
    }
    
    //build메서드
    public Employee build(){
      return new Employee(this);
    }
  }
}
```



이 코드를 기반으로 코드를 짜면 다음과 같다.

```java
Employee kim = new Employee.Builder("kim", "backend").age(12).company("naver").build();
```

