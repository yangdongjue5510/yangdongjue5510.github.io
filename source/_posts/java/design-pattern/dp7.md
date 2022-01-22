---
title: 7. 어댑터 패턴
date: 2021-10-09 18:33:00
tags:
category:
    - Java
    - Design Pattern
---
## 어댑터 패턴
한 클래스의 인터페이스를 사용하고자 한 다른 인터페이스로 변환한다.
쓰고자 하는 인터페이스를 구현한 클래스에 바꾸고 싶은 클래스의 객체를 구성한다.

![](/img/dp/dp7-1.png)

닭과 오리를 만들어서 날고 울 수 있게 하고 싶다.
그러나 두 객체의 행동이 약간 다르다.
```java
public interface Chicken {
    void kokkio();
    void fly();
}
```
```java
public interface Duck {
    void quak();
    void fly();
}
```

이 두 인터페이스를 구현하는 두 클래스가 있다.
```java
public class KoreanChicken implements Chicken{
    @Override
    public void kokkio() {
        System.out.println("꼬끼오!!!");
    }

    @Override
    public void fly() {
        System.out.println("푸득...");
    }
}
```
```java
public class KoreanDuck implements Duck{
    @Override
    public void quak() {
        System.out.println("꽥!!");
    }

    @Override
    public void fly() {
        System.out.println("푸드득~!");
    }
}
```

이제 닭을 오리처럼 행동을 요구하고 싶다.
**오리 인터페이스를 구현하는 어댑터**를 만들어보자
이 어댑터는 **치킨 객체를 구성**하고 있다.
```java
public class ChickenAdapter implements Duck{
    Chicken chicken;

    public ChickenAdapter(Chicken chicken){
        this.chicken = chicken;
    }
    @Override
    public void quak() {
        chicken.kokkio();
    }

    @Override
    public void fly() {
        chicken.fly();
    }
}
```


이제 어댑터를 사용해서 오리와 닭을 둘다 동일한 메소드로 요청하자!
```java
public class Test {
    public static void main(String[] args) {
        Duck koreanDuck = new KoreanDuck();
        koreanDuck.quak();
        koreanDuck.fly();

        Chicken koreanChicken = new KoreanChicken();
        Duck adaptedChicken = new ChickenAdapter(koreanChicken);
        adaptedChicken.quak();
        adaptedChicken.fly();
    }
}
```