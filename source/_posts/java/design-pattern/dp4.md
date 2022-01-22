---
title: 4. 팩토리 패턴
date: 2021-08-18 09:54:52
tags:
category:
    - Java
    - Design Pattern
---
## 팩토리 메서드 패턴
인스턴스 생성에 대한 인터페이스(일반적 의미)를 만들어, 서브 클래스에서 어떤 클래스의 인스턴스를 만들지 결정하도록 설계하는 것.


## 추상 팩토리 패턴
구현된 클래스 없이 관련된 클래스들을 만드는 인터페이스를 제공하는 패턴

> 배울 내용 요약

#### 추상화에 의존해라. 구현된 클래스에 의존하지 말라.


> 예시


#### 간단한 팩토리 예제
신발 가게에서 신발 종류에 따라 생성해서 포장해보자!


먼저 신발 추상 클래스를 만들어보자.
(얘를 상속해서 종류에 따라 만들거다.)
```java
package Factory;

public abstract class Shoes {
    String modelName;
    int price;

    public abstract void prepare();
    public abstract void wrap();
    public abstract void box();
}

package Factory;

public class BasketballShoes extends Shoes{

    @Override
    public void prepare() {
        System.out.println("ready for the basketball shoes.");
    }

    @Override
    public void wrap() {
        System.out.println("wrapping basketball shoes");
    }

    @Override
    public void box() {
        System.out.println("boxing basketball shoes");
    }
}

package Factory;

public class FootballShoes extends Shoes{
    @Override
    public void prepare() {
        System.out.println("prepare for football shoes");
    }

    @Override
    public void wrap() {
        System.out.println("wrapping football shoes");
    }

    @Override
    public void box() {
        System.out.println("boxing football shoes");
    }
}

```


이제 객체를 만들 팩토리 클래스를 만들어보자.
```java
package Factory;

public class SimpleNikeFactory {
    public Shoes createShoes (String type){
        Shoes shoes = null;
        if (type.equals("basketball")){
            shoes = new BasketballShoes();
        } else if (type.equals("football")){
            shoes = new FootballShoes();
        }
        return shoes;
    }
}
```
자세히 살펴보면, new가 안에 들어있다.


이 팩토리 클래스를 사용하는 신발가게 클래스를 만들어보자.
```java
package Factory;

public class ShoeStore {
    SimpleNikeFactory factory;

    public ShoeStore(SimpleNikeFactory factory){
        this.factory = factory;
    }

    public Shoes orderShoes(String type) {
        Shoes shoes;
        shoes = factory.createShoes(type);

        shoes.prepare();
        shoes.wrap();
        shoes.box();
        return shoes;
    }
}
```
이 경우도 팩토리와 가게가 HAS-A 관계임을 알 수 있다.


간단하게 결과를 알아보자.
```java
package Factory;

public class Tester {
    public static void main(String[] args) {
        SimpleNikeFactory factory = new SimpleNikeFactory();
        ShoeStore store1 = new ShoeStore(factory);
        store1.orderShoes("basketball");
        store1.orderShoes("football");
    }
}
```

이 정도가 간단한 예시고, 팩토리 메서드 패턴과 추상 팩토리 패턴을 더 알아보자.


#### 팩토리 메서드 패턴 예제
자, 이제 매장이 나이키 매장과 아디다스 매장이 있다고 가정하자.
나이키 매장과 아디다스 매장마다 각자의 보여줄 메시지와 사은품으로 줄 아이템이 다르다고 할 때,
이를 코드로 구현해보자.


> 즉 어떤 객체들이 만들어지는 과정과 가진 요소들을 분류해서 캡슐화!


일단 신발을 기본적으로 가져야 할 내용을 추상 클래스로 만들어보자
```java
package Factory;

import java.util.ArrayList;

public abstract class Shoes {
    String modelName;
    String cushion;
    String signaturePlayer;
    ArrayList<String> shoeItem = new ArrayList<>();
    public void prepare(){
        System.out.println("ready for "+ modelName);
        System.out.println("ready for "+ cushion);
        System.out.println("this shoes are for "+ signaturePlayer);
        for (String item : shoeItem){
            System.out.println("add "+item);
        }
    }
    public String getModelName(){
        return this.modelName;
    }
    public abstract void wrap();
    public abstract void box();
}
```


그리고 신발을 팔 가게들도 필요한 역할을 나타내는 추상 클래스도 만들어보자
```java
package Factory;

public abstract class ShoeStore {

    public Shoes orderShoes(String type) {
        Shoes shoes;
        shoes = createShoes(type);

        shoes.prepare();
        shoes.wrap();
        shoes.box();
        return shoes;
    }
    public abstract Shoes createShoes(String type);
}
```


신발 클래스를 상속해서 우리가 만들고 싶은 구체적인 신발 클래스를 만들자
```java
package Factory;

public class NikeFootballShoes extends Shoes{
    public NikeFootballShoes(){
        this.modelName = "Nike Football";
        this.cushion = "NO cushion";
        this.signaturePlayer = "Ronaldo";
        this.shoeItem.add("nike football sticker");
    }
    @Override
    public void wrap() {
        System.out.println("wrapping Nike football shoes");
    }

    @Override
    public void box() {
        System.out.println("boxing Nike football shoes");
    }
}

package Factory;

public class AdidasBasketballShoes extends Shoes{
    public AdidasBasketballShoes(){
        this.modelName = "Adidas basketball";
        this.cushion = "Boost cushion";
        this.signaturePlayer = "Lillard";
        this.shoeItem.add("Adidas shoe string");
    }
    @Override
    public void wrap() {
        System.out.println("wrapping Adidas basketball shoes");
    }

    @Override
    public void box() {
        System.out.println("boxing Adidas basketball shoes");
    }
}
```


이제 우리가 만들 신발 클래스를 만들었으니, 이 신발들을 만들 팩토리 클래스를 작성해보자.
팩토리 클래스는 신발 가게 추상 클래스를 상속해서 만든다.
```java
package Factory;

public class NikeShoeStore extends ShoeStore{
    public Shoes createShoes (String type){
        Shoes shoes = null;
        if (type.equals("basketball")){
            shoes = new NikeBasketballShoes();
        } else if (type.equals("football")){
            shoes = new NikeFootballShoes();
        }
        return shoes;
    }
}

package Factory;

public class AdidasShoeStore extends ShoeStore{
    @Override
    public Shoes createShoes(String type) {
        Shoes shoes = null;
        if(type.equals("basketball")) {
            shoes = new AdidasBasketballShoes();
        } else if(type.equals("football")){
            shoes = new AdidasFootballShoes();
        }
        return shoes;
    }
}
```


이제 잘 돌아가는지 확인해보자
```java
package Factory;

public class Tester {
    public static void main(String[] args) {
        ShoeStore nikeShoeStore = new NikeShoeStore();
        ShoeStore adidasShoeStore= new AdidasShoeStore();
        adidasShoeStore.orderShoes("basketball");
        System.out.println();
        nikeShoeStore.orderShoes("football");
    }
}
```

> 결론

결론을 말하자면 **자신이 만들고 있는 클래스가 바뀔 가능성이 있다면 팩토리 메소드 패턴 같은 기법을 써서 변경될 수 있는 부분을 캡슐화 하여야 한다.**


신발공장 -(의존)-> 신발 이었던 것을
개별 신발 공장 -> 신발공장 추상클래스 가 되고,
신발공장 추상 클래스 -> 신발 추상 클래스 이 되고,
개별 신발 클래스 -> 신발 추상 클래스 (의존성 뒤집기)이 된다.


즉 신발 특성이 신발 종류와 제조사에 따라 다르고, 신발 제작 방식이 제조사마다 다르므로
신발과 신발 팩토리를 추상화해서 만들었다.


#### 추상 팩토리 예제
신발과 신발 공장 뿐만 아니라, 신발 재료 공장을 따로 만들어보자.
신발 요소는 재조사 별로 사용하는 재료가 다를 수 있다고 가정할때,
nike의 신발 요소 공장을 만들어봤다.

```java
package Factory;

public interface ShoeIngredientFactory {
    public Shoelace createShoelace();
    public Outsole createOutsole();
    public Cushion creatCushion();
    public Colors[] createColors();
}

package Factory;

public class NikeShoeIngredientFactory implements ShoeIngredientFactory{

    @Override
    public Shoelace createShoelace() {
        return new NikeShoelace();
    }

    @Override
    public Outsole createOutsole() {
        return NikeOutsole();
    }

    @Override
    public Cushion creatCushion() {
        return NikeCushion();
    }

    @Override
    public Colors[] createColors() {
        return new Colors[] = {new BredColor()};
    }

}


이제 요소 팩토리에서 만든 요소들을 통해 신발을 만드는 신발 추상클래스를 만들자.
이때 신발클래스는 요소 팩토리를 조합해서 만들어진다!
```java
package Factory;

public abstract class Shoes {
    String modelName;
    Cushion cushion;
    Outsole outsole;
    Shoelace shoelace;
    Colors colors[];
    public abstract void prepare();

    public String getModelName(){
        return this.modelName;
    }
    public void setModelName(String str){
        this.modelName = str;
    }
    public void wrap(){
        System.out.println("wrapping...");
    }
    public void box(){
        System.out.println("boxing...");
    }
}

package Factory;

public class BasketballShoes extends Shoes {
    ShoeIngredientFactory shoeIngredientFactory;

    public BasketballShoes(ShoeIngredientFactory shoeIngredientFactory) {
        this.shoeIngredientFactory = shoeIngredientFactory;
    }

    @Override
    public void prepare() {
        this.cushion = shoeIngredientFactory.creatCushion();
        this.shoelace = shoeIngredientFactory.createShoelace();
        this.outsole = shoeIngredientFactory.createOutsole();
    }
}
```


자 이제 나이키 신발 요소 공장에서 신발 요소들을 만들어서 원하는 신발을 제작할 수 있는 나이키 매장을 만들어보자.
(신발 가게 클래스는 이전과 동일.)
```java
package Factory;

public class NikeShoeStore extends ShoeStore{
    public Shoes createShoes (String type){
        Shoes shoes = null;
        ShoeIngredientFactory shoeIngredientFactory = new NikeShoeIngredientFactory()

        if (type.equals("basketball")){
            shoes = new NikeBasketballShoes(shoeIngredientFactory);
            shoes.setModelName("basketball");
        } else if (type.equals("football")){
            shoes = new NikeFootballShoes(shoeIngredientFactory);
            shoes.setModelName("football");
        }
        return shoes;
    }
}
```


> 후! 힘들지만 다 살펴봤다!