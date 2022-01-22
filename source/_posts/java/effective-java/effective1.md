---
title: 생성자 대신 정적 팩터리 메서드를 고려하라.
date: 2021-10-15 22:38:49
tags:
  - Effective Java
category:
  - Java
---

[정적 팩터리 메서드란 무엇인가](#정적-팩터리-메서드란-무엇인가)
[정적 팩터리 메서드의 장점은 무엇인가](#정적-팩터리-메서드의-장점은-무엇인가?)
[정적 팩터리 메서드의 단점은 무엇인가](#정적-팩터리-메서드의-단점은-무엇인가?)
[정적 팩터리 메서드 네이밍 컨벤션](#정적-팩터리-메서드-네이밍-컨벤션)



일반적으로 public 생성자를 통해 클래스의 인스턴스를 만든다.
하지만 정적 팩터리 메서드를 통해 만들면 여러 이점이 있다.

### 정적 팩터리 메서드란 무엇인가?

static으로 선언되었고, 객체를 반환하는 메서드를 말한다.

책에 나온 예시인 valueOf를 보자

```java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```

이렇게 정적으로(static) 선언되어서 다음같이 사용할 수 있다.

```java
boolean bool = true;
Boolean boolWrapper = Boolean.valueOf(bool);
```



### 정적 팩터리 메서드의 장점은 무엇인가?

1. **이름을 가질 수 있다.**

   - 생성자는 여러 매개변수를 가질 수 있지만 이름은 클래스명이 전부라 구분이 어렵다.
   - 정적팩터리는 메소드의 이름을 다양하게 할 수 있어서 구분을 쉽게 할 수 있다.

   ```java
   class Student{
     int age;
     double score;
     
     //정적 팩토리 메서드
     static Student ageOf(int age){
       return new Student(age);
     }
     static Student scoreOf(double score){
       return new Student(score)
     }
     
     //생성자
     public Student(int age){ this.age =age;}
     public Student(double score){ this.score = score;}
   }
   
   
   //아래 매개변수로 판단하기는 헷갈린다!
   Student a = new Student(30);
   Studnet b = new Student(4.5);
   
   //메소드 명을 보니 분명하게 판별할 수 있다!
   Student c = Student.ageOf(30);
   Student d = Student.scoreOf(4.5);
   
   ```

   

2. **호출 될 때마다 인스턴스를 새로 생성하지 않아도 된다.**

   - 위 valueOf 메소드는 미리 만들어진 static Boolean 객체를 반환한다.

   - 정적 팩토리 메서드는 객체를 요청할 때 굳이 객체를 만들어 주지 않아도 된다.(싱글톤처럼 활용도 가능하다.)

     ```java
     class Boolean{
       //정적 상수 객체들
       public static final Boolean TRUE = new Boolean(true);
       public static final Boolean FALSE = new Boolean(false);
       
       //static final 객체를 반환하는 정적 팩터리 메서드
       public static Boolean valueOf(boolean b){
       	return b ? Boolean.TRUE : Boolean.FALSE;
     	}
     }
     ```

     

3. **반환 타입의 하위 타입 객체를 반환할 수 있다.**

   - 인터페이스에 정적 메서드를 선언하면 구현 클래스에 대한 내용을 공개 안해도 그 객체를 반환 할 수 있다.

     ```java
     public interface Person{
       public static Person whoTeaches(){
         return new Teacher();
       }
       public static Person whoLearns(){
         return new Student();
       }
     }
     ```

      ```java
      public class Student implements Person{...}
      public class Teacher implements Person{...}
      ```

     ```java
     //Teacher 클래스나 Student 클래스를 몰라도 해당 클래스 객체들을 사용할 수 있다!!
     Person kim = Person.whoTeaches();
     Person lee = Person.whoLearns();
     ```

     

4. **매개변수에 따라 매번 다른 객체를 반환할 수 있다.**

   - 인터페이스의 정적 팩터리 메서드의 반환 객체가 인터페이스를 구현하기만 하면 어떻게 되든 상관없다.(캡슐화.)

   - 인터페이스를 구현한 구현체를 반환하는 규칙만 지킨다면, 추후에 수정하기도 용이하다.

     ```java
     public interface Person{
       public static Person isOld(int age){
         if (age>65) return new OldPerson(int age);
         else return new YoungPerson(int age);
       }
     }
     ```

     ```java
     public class OldPerson implements Person{
       int age;
       public OldPerson(int age){this.age = age;}
     }
     public class YoungPerson implements Person{
       int age;
       public YoungPerson(int age){this.age = age;}
     }
     ```

     ```java
     //사용자는 어떤 객체가 만들어지는지 알 필요가 없다.
     Person kim = Person.isOld(75); //OldPerson객체
     Person lee = Person.isOld(23); //YoungPerson객체
     ```



### 정적 팩터리 메서드의 단점은 무엇인가?

1. **public이나 protected 생성자 대신 정적 팩터리 메서드를 사용하면, 상속이 불가능해진다.**
   - 상속 관계를 만들려면 public, protected 생성자가 있어야 한다.
2. **개발자가 이해하기 어렵다.**
   - 정적 팩터리 메서드는 클래스나 인터페이스에 있어서 생성자보다는 알기 어렵다.



### 정적 팩터리 메서드 네이밍 컨벤션

- **from** : 매개변수 하나 받아서 해당 타입의 인스턴스 반환
- **of** : 여러 매개변수 받아 적합한 타입의 인스턴스 반환
- **valueOf** : from, of의 더 자세한 버전
- **getInstance** : (매개변수를 받아 매개변수를 가진) 인스턴스를 반환한다.
- **create / newInstance** : 새로운 인스턴스를 만들어 반환한다.
  - instance 대신 다른 클래스의 이름을 적으면 다른 클래스의 인스턴스를 반환한다.

