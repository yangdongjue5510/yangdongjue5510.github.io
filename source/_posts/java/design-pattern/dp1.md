---
title: 1. 전략 패턴
date: 2021-08-12 19:24:17
tags:
category:
    - Java
    - Design Pattern
---
## 전략 패턴
변경 가능하고 독립적인 알고리즘의 구성으로 만든 패턴

- 특징
  - 특정한 계열의 알고리즘을 정의
  - 각 알고리즘을 캡슐화
  - 이 알고리즘들을 해당 계열 안에서 상호 교체 가능

![기본적인 틀](/img/dp/dp1-1.png)

> 배울 내용 요약

#### 1. 다양하게 변화할 법한 내용은 캡슐화하자.


#### 2. 구현이 아닌 인터페이스로 프로그래밍하자.


#### 3. 상속보다는 구성(IS-A, HAS-A, 구현을 구분하라)
- IS-A는 상속 관계로 표현되고, HAS-A는 구성으로 표현된다.
- 우리는 상속보다 구성을 택할 경우, 런타임 중에 동적으로 행동을 줄 수 있다.


> 예제로 알아보자

목적 : 다양한 농구 선수들이 플레이 내용을 출력하는 프로그램을 만들어보자

요구사항 :

1. 농구 선수는 여러 포지션을 가질 수 있다.
2. 농구 선수는 모두 슛과 드리블을 할 수 있다.
3. 각 포지션 마다 독특한 슛과 드리블을 구사할 수 있다.
4. 특정 농구선수는 여러 슛과 드리블을 구사할 수 있다.

**이제 슛과 드리블을 인터페이스로 캡슐화 해서 구현해보자**

**player class**

```java
package Strategy;

public abstract class Player {
    DribbleBehavior dribbleBehavior;
    ShootBehavior shootBehavior;
    public abstract void display();
    
    //dynamically
    public void setShootBehavior(ShootBehavior sb){ shootBehavior = sb; }
    public void setDribbleBehavior(DribbleBehavior db){ dribbleBehavior = db; }
    
    public void performDribble(){
        dribbleBehavior.dribble();
    }
    public void performShoot(){
        shootBehavior.shoot();
    }
}
```

**이제 인터페이스를 만들어서 캡슐화해보자~**

```java
package Strategy;

public interface DribbleBehavior {
    public void dribble();
}

package Strategy;

public interface ShootBehavior {
    public void shoot();
}
```

**이제 인터페이스에 따라 다양한 슛과 드리블 클래스를 만들자**

```java
package Strategy;

public class ThreePointShoot implements ShootBehavior{
    @Override
    public void shoot(){
        System.out.println("from the downtown... It's good!!!!!!");
    }
}

package Strategy;

public class DunkShoot implements ShootBehavior{
    public void shoot(){
        System.out.println("here comes the hammer!!!!!");
    }
}


package Strategy;

public class CrossOverDribble implements DribbleBehavior{
    @Override
    public void dribble() {
        System.out.println("what a dribble... wazzle dazzle!");
    }
}


package Strategy;

public class HegiDribble implements DribbleBehavior{
    @Override
    public void dribble() {
        System.out.println("look at that hegi..");
    }
}

```

이제 player 클래스를 구현하는 포지션 별 행동을 잘 구성하자!
```java
package Strategy;

public class Guard extends Player{
    public Guard(){
        dribbleBehavior = new CrossOverDribble();
        shootBehavior = new ThreePointShoot();
    }
    @Override
    public void display() {
        System.out.println("Here comes new Guard!");
    }
}
package Strategy;

public class Foward extends Player{
    public Foward(){
        dribbleBehavior = new PowerDribble();
        shootBehavior = new DunkShoot();
    }

    @Override
    public void display() {
        System.out.println("Here comes new Forward...!");
    }
}
```

자 이제 잘 작동하는지 한번 해보자.
```java
package Strategy;

public class MiniPlayerSimulator {
    public static void main(String[] args){
        Player curry = new Guard();
        curry.display();
        curry.performDribble();
        //dynamically setter
        curry.setDribbleBehavior(new HegiDribble());
        curry.performDribble();
        curry.performShoot();

        Player james = new Foward();
        james.display();
        james.performDribble();
        james.performShoot();
    }
}
```

> 배운 내용 확인하기

#### 1. 다양하게 변화할 법한 내용은 캡슐화하자.
- 슛과 드리블은 다양하게 나뉘므로 선수 클래스로부터 캡슐화 했다

#### 2. 구현이 아닌 인터페이스로 프로그래밍하자.
- 슛과 드리블은 오버라이딩 같은 방식이 아닌, 인터페이스로 다향성을 구현했다.

#### 3. 상속보다는 구성(IS-A, HAS-A, 구현을 구분하라)
- 가드가 삼점 슛과 크로스오버를 가진 것처럼 구성하였다.