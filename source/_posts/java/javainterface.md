---
title: 인터페이스와 추상클래스의 차이(feat. 데코레이터 패턴)
date: 2021-11-07 19:02:30
tags:
category:
    - Java
---

인터페이스와 추상 클래스의 차이를 잘 느낄 수 있는 예제를 가져왔다.

데코레이터를 인터페이스로 구현해본다고 하자.



## 인터페이스로 데코레이터를 구현한다면?

패션 모델에게 각종 의류를 데코레이터로 적용하는 예시를 인터페이스로 구현해보자.

```java
//모델의 역할은 런웨이를 하는 것.
public interface Model {
    void runway();
}

```

```java
//모델인터페이스를 구현한 한국모델 구현체(클래스)
public class KoreanModel implements Model{
    @Override
    public void runway() {
        System.out.println("한국 모델이 런웨이를 시작합니다.");
    }
}
```

모델과 구현체를 만들었다.

이제 데코레이터 인터페이스를 도입해보자.
데코레이터는 모델을 포함하고 있으며 모델을 대신할 것이다.

```java
//의류데코레이터는 모델이 옷을 입도록 한다.
public interface ClotheDecorator extends Model{
    void wearClothes();
}

```

```java
//의류 데코레이터를 구현한 의류 클래스
public class Shirts implements ClotheDecorator{
    Model model;
    public Shirts(Model model){
        this.model = model;
    }
  	//우리가 원하는 추가 기능이다.
    @Override
    public void wearClothes() {
        System.out.println("셔츠를 입었습니다!");
    }
		
  	//모델을 대신해야 하기 때문에 이 메소드가 구현되어야 한다.
    @Override
    public void runway() {
        model.runway();
        wearClothes();
    }
}
```

```java
//이번엔 바지를 구현해보자! 전체적인 방식은 비슷하다.
public class Pants implements ClotheDecorator{
    Model model;
    public Pants(Model model){
        this.model = model;
    }
    @Override
    public void wearClothes() {
        System.out.println("바지를 입었습니다!!");
    }

    @Override
    public void runway() {
        model.runway();
        wearClothes();
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Model yang = new Pants(new Shirts(new KoreanModel()));
        yang.runway();
    }
}
```



> 근데 자세히 보면 Shirts와 Pants에 중복된 부분이 있다...!

```java
	Model model;
	public Pants(Model model){
		this.model = model;
	}
```

이 부분이 중복된다.

Model model을 인터페이스에 넣으려면 반드시 상수가 되어버리니 우리가 원래 사용하려던 방식과 안맞는다...
그리고 생성자를 인터페이스에 추가해줄 수도 없다....



## 추상클래스로 중복된 부분을 제거해보자.

데코레이터 인터페이스를 데코레이터 추상 클래스로 바꿔보자.

```java
public abstract class ClothDecorator implements Model {
    Model model;

    public ClothDecorator(Model model){
        this.model = model;
    }
    public abstract void wearCloth();

    @Override
    public void runway(){
        model.runway();
        wearCloth();
    }
}
```

이 추상클래스를 자세히보면, **두가지 역할**을 한다.

1. 멤버 변수 모델을 생성자로 초기화
2. runway메소드의 로직을 구현

그래서 설계가 뻣뻣해지는 경향이 있다.



하지만 추상 클래스로 데코레이터를 작성하면 구현 클래스들이 좀 더 간략해지긴 한다.

```java
public class Hat extends ClothDecorator{
    public Hat(Model model) { super(model); }

    @Override
    public void wearCloth() { System.out.println("모자를 썼습니다!!!"); }
}
```

```java
public class Shoes extends ClothDecorator{
    public Shoes(Model model) { super(model); }

    @Override
    public void wearCloth() { System.out.println("신발을 신었습니다!!!"); }
}
```

멤버 변수 선언과 로직 구현을 추상클래스가 해주니 구현 클래스가 간략해졌다.

**하지만 추상 클래스가 너무 많은 역할을 하는 건 아닌지 생각해봐야 한다!**

