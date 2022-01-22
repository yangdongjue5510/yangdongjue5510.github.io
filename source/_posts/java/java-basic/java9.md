---
title: 9. 어노테이션
date: 2021-09-05 09:48:35
tags:
category:
    - Java
    - Java Basic
---
### 어노테이션
어노테이션은 자바 소스코드에 추가적인 정보를 제공하는 메타데이터이다.
어노테이션 선언 방법을 알아보자


### 1. 마커 어노테이션
표시만 해두는 어노테이션. 메서드 없이 선언하면 마커 어노테이션이 된다.
@Override, @Deprecated 가 대표적 예시.
```java
public @interface Marker{}
```
이렇게 선언을 해놓으면,
```java
@Marker
public class Main(String[] args){}
```


### 2. single value 어노테이션
하나의 값을 입력받을 수 있는 어노테이션.
```java
public @interface Single{
    int value(); //이 변수의 이름은 반드시 value
    //int[] value(); 이렇게 하면 여러 int값을 받을 수 있다.
}
```
이런 식으로 선언할 수 있다.


```java
package annotation;
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@interface Single{
    int value();
}
public class Main {
    @Single(10)
    public static void myMethod() throws NoSuchMethodException {
        Main obj = new Main();
        Method m = obj.getClass().getMethod("myMethod");
        Single single = m.getAnnotation(Single.class);
        System.out.println(single.value());
    }

    public static void main(String[] args) throws NoSuchMethodException {
        myMethod();
    }
}
```
이렇게 사용할 수 있다!


### 3. multi value 오너테이션
여러 개의 값을 저장할 수 있는 어노테이션이다.
```java
public @interface MultiValue{
    int id();
    String name() default "yang";
    String[] roles() default {"Student", "backend"};
}
```
이런 식으로 선언할 수 있다.
```java
@MultiValue(id = 1, name = "yang", roles = {"a", "b"})
public void myMethod()...
```
이런식으로 활용할 수 있다.