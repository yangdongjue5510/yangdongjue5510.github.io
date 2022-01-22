---
title: 3. 데코레이터 패턴
date: 2021-08-17 09:14:02
tags:
category:
    - Java
    - Design Pattern
---
## 데코레이터 패턴
동적으로 객체에 부가적인 책임을 더하는 패턴.
상속으로 확장하는 것이 아닌, 객체들의 결합으로 역할을 확장한다.
![](/img/dp/dp3-1.png)

> 배울 내용 요약

#### 클래스들은 코드 수정없이 새로운 동작과 협동할 수 있도록 확장되어야 한다.


> 예제

음료와 첨가물에 따라 가격과 음료 내용을 출력하는 프로그램을 만들어보자


먼저 음료를 추상화한 클래스를 만들어보자.
```java
package Decorator;

public abstract class Beverage {
    String description = "Unknown Beverage";
    public String getDescription(){
        return description;
    }
    public abstract double cost();
}
```


첨가물을 추상화한 클래스를 만들어보자.
이 클래스가 데코레이터 역할을 추상화하게 된다.
이때 주목할 점은, **데코레이터도 Beverage임을 기억하라.**
```java
package Decorator;

public abstract class CondimentDecorator extends Beverage{
    public abstract String getDescription();
}
```


음료 클래스를 구체화하는 에스프레소, 하우스블랜드 클래스를 만들어보자.
```java
package Decorator;

public class Espresso extends Beverage{
    public Espresso(){
        description = "Espresso";
    }
    public double cost(){
        return 1.99;
    }
}

package Decorator;

public class HouseBlend extends Beverage{
    public HouseBlend(){
        description = "House Blend COffee";
    }
    public double cost(){
        return .89;
    }
}
```


첨가물 클래스를 추상화하는 모카 클래스를 만들어보자.
생성자를 잘 보면, 장식할 클래스를 인자로 가져온다는 걸 알 수 있다.
```java
package Decorator;

public class Mocha extends CondimentDecorator{
    Beverage beverage;

    public Mocha(Beverage beverage){
        this.beverage = beverage;
    }
    public String getDescription(){
        return beverage.getDescription() + ", Mocha";
    }
    public double cost(){
        return .20 + beverage.cost();
    }
}
```


이제 잘 되는지 실험해보자!
```java
package Decorator;

public class StarbuzzCoffee {
    public static void main(String[] args) {
        Beverage beverage = new Espresso();
        System.out.println(beverage.getDescription() + "$" + beverage.cost());

        Beverage beverage2 = new HouseBlend();
        beverage2 = new Mocha(beverage2);
        System.out.println(beverage2.getDescription() + "$" + beverage.cost());
    }
}
```

> 배운 내용 확인하기

#### 클래스들은 코드 수정없이 새로운 동작과 협동할 수 있도록 확장되어야 한다.
기존의 음료에 첨가물을 추가할 때, 상속으로 새로운 클래스로 만들 필요가 없이, 그냥 데코레이터로 기존 음료를 인수로 전달하면 된다.