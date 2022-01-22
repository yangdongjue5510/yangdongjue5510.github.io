---
title: 3. 다형성, 상속, 결합, 다운 캐스팅
date: 2021-08-17 13:06:28
tags:
category:
    - Java
    - Java Basic
---
## 다형성
하나의 코드가 여러 자료형으로 구현되어 실행되는 것.
같은 코드에서 상황에 따라 다른 결과가 반환.
상위 클래스에서 공통 부분을 구현하고, 하위 클래스에서 각 클래스에 맞는 기능 구현
여러 클래스를 하나의 타입(상위 클래스)로 핸들링 가능


## 상속은 언제 사용할까


#### IS-A관계(inheritance, 상속)
- 일반적(상위) vs 구체적(하위) 관계
- 상위 클래스 수정이 하위 클래스에 영향을 크게 미침
- 상속은 클래스간의 결합도 높음
- 계층구조가 복잡하면 비추.


#### HAS-A관계(composition)
- 클래스가 다른 클래스를 포함하는 관계(변수로 선언)
- 상속을 사용하지 않아 결합도 낮은 편.


## 다운 캐스팅
- 업캐스팅된 클래스를 다시 원래의 타입으로 형 반환(원래 자기 클래스로)
- 하위로 형 변환은 명시적으로 해야 함
```java
Customer vc = new VIPCustomer();
VIPCustomer vCustomper = (VIPCustomer)vc;
```
변환할 때, vc가 VIPCustomer의 인스턴스가 아닌데 형변환을 하려고 한다면 에러가 일어난다.


#### 원래 인스턴스의 형이 맞는지 확인하는 instanceof
```java
Customer vc = new GoldCustomer();
if (vc instanceof VIPCustomer){
    VIPCustomer vCustomper = (VIPCustomer)vc;
}
```
위에서 언급한 오류 가능성을 체크하여 다운캐스팅하는 예시이다.