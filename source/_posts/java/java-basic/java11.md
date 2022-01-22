---
title: 자바 코딩 규칙(Java Code Conventions)
date: 2021-11-20 23:23:18
tags:
category:
    - Java
    - Java Basic
---

## 자바 소스 파일 구조

**시작 주석**

**package 문**

**import 문**

**Class 혹은 Interface 선언**



## 시작 주석

모든 소스 파일은 다음과 같은 시작 주석을 써줘야 한다.

```java
/*
 *클래스 이름
 *
 *버전 정보
 *
 *날짜
 *
 *저작권 주의
 */
```



## package문 import문

일반적으로 패키지문을 먼저 쓰고, 임포트문을 다음에 써준다.

두 문 사이에는 공백을 써준다.

```java
package com.yangdongjue;

import java.util.*;
```



## class와 interface 선언

클래스와 인터페이스 선언 순서를 알아보자

1. **문서화 주석**(/** ... */)
2. **클래스/인터페이스 문**(public class ...)
3. **구현 주석**(/* ... */)
4. **클래스(static) 변수**
   public - protected - default - private 순으로 나열
5. **일반 변수**
   클래스 변수와 동일하게 나열
6. **생성자**
7. **메서드**
   접근자가 아닌 기능에 의해 구성되어야 함.



> 한줄의 길이

한줄의 길이는 최대 80자다.

만약 하나의 식이 한 줄에 안들어가면? 줄 나누기를 해주자.



## 줄나누기

1. **콤마 후에 두 줄로 나눈다.**

   ```java
   someMethod(VeryLong veryLongExpress1, VeryLong veryLongExpress2,
       VeryLong veryLongExpress3);
   ```

2. **연산자 앞에 두 줄로 나눈다.**
   이때 연산자 레벨이 맞도록 나눠 줘야 한다. (괄호의 경우...)

   ```java
   VeryLong veryLong = veryLong1+(veryLong2+veryLong3)
       +veryLong4;
   ```

3. **메서드 선언에서 줄 나누기를 할 때는 한번 더 들여써준다.**
   메서드 구현부와 헷갈리면 안되기 때문이다.

   ```java
   someMethod(VeryLong veryLongExpress1, VeryLong veryLongExpress2,
           VeryLong veryLongExpress3) {
       ...(메소드 구현부)  
   }
   ```

   

## 주석

주석은 코드 개요와 코드 자체로는 알 수 없는 추가 정보를 제공하기 위해서 쓰인다.
주석은 프로그램을 이해하기 위한 내용을 다뤄야 한다.
코드 상에 이미 표현된 중복 정보는 다루지 말아야 한다.

자바 주석은 구현 주석과 문서화 주석으로 나뉜다.

### 구현 주석

1. **블록 주석**
   파일, 메서드, 클래스 등에 설명 할 때 사용.
   클래스나 인터페이스 속에서 파일이나 메서드가 시작되기 전에 써줘야 하고,
   해당 메서드나 파일의 들여쓰기를 반영해야 한다.

   ```java
   /*
    *블록 주석을 작성하자~
    */
   ```

2. **한 줄 주석**
   짧은 주석은 뒤에 따라오는 코드를 설명.
   뒤 코드와 동일한 들여쓰기 적용.
   윗 코드와 한칸 띄어서 사용

   ```java
   if (condition) {
     
     /* 한 줄 주석 */
     int a = 1;
   }
   ```

3. **꼬리 주석**
   아주 짧은 주석이 필요한 경우 설명하는 코드와 같은 줄에 작성.

   ```java
   if (condition) {
     return true;		/* condition에 해당 */
   } else {
     return false		/* condition에 해당히지 않음 */
   }
   ```

4. **줄 끝 주석**
   한 줄을 통째로 주석 처리하거나, 일부를 주석 처리 할때 사용.
   코드를 주석처리 할 때 여러줄을 주석하는 건 가능하지만, 그외는 여러줄 주석을 금한다.

   ```java
   if (condition) {
     
     //줄 끝 주석은 이렇게 쓸 수 있다.
     int a = 1;
   } 
   else {
     return false; 		//줄 끝 주석을 여기에도 쓸 수 있다.
   }
   //if (condition) {
   //  int a = 2;
   //}
   ```

   

### 문서화 주석

문서화 주석은 다음 다섯가지를 분석한다.

1. **자바 클래스**
2. **인터페이스**
3. **생성자**
4. **메서드**
5. **필드**

다만 문서화 주석이 어울리지 않으면, 클래스 선언 후에 블록 주석 혹은 한줄 주석으로 표현한다.

```java
/**
 * 이 클래스는 어떤 역할을 합니다.
 */
public class Example {
  ...
}
```



## 변수

### 변수 선언

```java
/* 옳은 예시 */
int a;
int b;

/* 괜찮지만 불편한 방식 */
int a, b;
```



이때 중요한 건 한 클래스에 필요한 변수를 선언은 가장 처음에 하는게 좋다!!!!!
그리고 지역 변수를 전역 변수와 같은 이름으로 선언하지 말자!!!!

```java
class Example {
  int a = 1;
  
  if (condition){
    int a = 2;	// 이렇게 쓰지 말라는 거다!!!
  }
}
```



## 클래스 & 인터페이스 선언

1. 메서드 이름과 파라미터를 감싸는 괄호 시작 사이에는 빈 공간이 없어야 한다.

   ```java
   public class Main {
     int a;
     int b;
     
     int method() {
       int c;
     }
   }
   ```

2. 여는 중괄호는 클래스&인터페이스&메서드의 선언부 문장 끝에 위치한다.

3. 닫는 중괄호는 구현부가 없는 경우가 아니면 여는 문장과 동일한 들여쓰기로 새로운 줄로 사용하자.

   ```java
   class Main {
     int a;
     int b;
   }
   
   class Okay {}
   
   class Wrong { int c; }
   ```

   

4. 클래스나 인터페이스 내부의 메서드는 분리되기 위해서 한 줄씩 띄어놔야 한다.

   ```java
   class Main {
     int a;
     int b;
     
     void firstMethod(){}
     
     void secondMethod(){}
   }
   ```

   
