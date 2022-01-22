---
title: private 생성자나 열거 타입으로 싱글턴임을 보증하라
date: 2021-10-22 00:54:57
tags:
  - Effective Java
category:
  - Java
---

### 싱글턴의 단점

싱글턴은 인스턴스를 하나만 생성할 수 있는 클래스를 말한다.
그런데 이 싱글턴 클래스를 사용하는 클라이언트를 테스트하기 어렵다.
특히 인터페이스를 구현하는 방식으로 만들어진 싱글턴이 아니면 mock 객체로 테스트 하기 어렵다.

> mock객체?

테스트하고 싶은 객체의 핵심 책임만 검증할 수 있도록 만들어진 객체이다.
원래 객체가 수많은 협력을 하고 있는데 반해, mock객체는 단순히 자신의 역할만 가지고 있을 뿐이다.
싱글톤은 자기 자신이 다른 수많은 객체와 협력해야 한다.(하나뿐이므로) 그래서 협력을 제외한 mock객체 테스트가 불가능하다는 의미다.



### public static final 방식

```java
public class Yang{
  public static final Yang INSTANCE = new YANG();
  private Yang(){...}
  
  ....
}
```

private 생성자는 맨 처음 public static final 인스턴스를 생성할 때 한번만 호출된다.

리플렉션 API의 AccessibleObject.setAccessible을 사용해서 private 생성자를 호출하는 경우가 있을 순 있는데,
그런 경우를 대비하면 생성자를 수정해서 예외처리를 하면된다.

1. 간결하게 싱글턴임을 알 수 있다.(static final이면 절대 다른 객체를 참조할 수 없으니..!)

### 정적 팩터리 메서드

```java
public class Yang{
  public static final Yang INSTANCE = new Yang();
  private Yang(){...}
  public static Yang getInstance(){return INSTANCE;}
  
  ....
}
```

위와 비슷하지만, 정적 팩터리 메서드를 추가했다.

이로 얻는 장점은

1. 싱글턴이 아니게 바꿀 수 있다. 
   - 접근하는 쓰레드마다 다른 객체를 반환해줄 수 있다! (쓰레드 식별해서 다르면 새 객체 만들고..)
2. 정적 팩터리 메서드를 제네릭 싱글턴 팩터리로 만들 수 있다!
   - 제네릭 팩터리로 만들어놓고 
