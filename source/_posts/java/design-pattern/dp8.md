---
title: 8. 상태 패턴
date: 2021-11-13 15:34:25
tags:
category:
    - Java
    - Design Pattern
---

# 상태 패턴

객체의 **특정 상태에 따라 객체의 행동이 달라지는 상황**에서,
객체의 상태를 본인이 체크해서 행동을 달리하는 것이 아니라,
**상태를 객체화해서 상태가 행동을 책임지도록 위임**하는 패턴.

특정 상태를 캡슐화하는 인터페이스를 선언하고,
원래 객체에서 해당 인터페이스를 구성하도록 설계한다.

상태 인터페이스를 구현한 여러 상태 객체들을 만들어서 활용한다.

## 활용 예시

자동차에는 여러 주행 모드가 있다.
우리가 설계하는 자동차 객체는 일반모드와 터보모드가 있다.

```java
//자동차의 역할을 캡슐화하는 자동차 인터페이스
public interface Car {
    void drive();
    void stop();
    void setState(BasicCarState state);
}
```



이제 자동차 인터페이스를 구현해보자.
이때 중요한건 **자동차의 상태를 구성하는 방식**으로 구현체를 설계해야 한다.

```java
//자동차 인터페이스를 구현한 기본자동차 구현체
public class BasicCar implements Car{
    private BasicCarState state;

    public BasicCar(){
        state = NormalState.getInstance();
    }
    @Override
    public void drive() {
        state.driveState(this);
    }

    @Override
    public void stop() {
        state.stopState(this);
    }

    @Override
    public void setState(BasicCarState state) {
        this.state = state;
    }
}
```



이제 자동차의 모드(상태)를 객체로 만들어보자.
일단 자동차의 모드와 관련된 행동을 캡슐화하자.

```java
//기본 자동차의 상태를 캡슐화하는 인터페이스
public interface BasicCarState {
    void driveState(Car car);
    void stopState(Car car);
}
```

캡슐화한 인터페이스를 구현해서 모드들을 만들어보자.
필자는 **상태 객체를 싱글톤으로 처리**했다.

```java
public class NormalState implements BasicCarState{
    private static NormalState normalState;

    private NormalState(){};

    public static BasicCarState getInstance(){
        if(normalState==null){
            normalState = new NormalState();
        }
        return normalState;
    }

    @Override
    public void driveState(Car car) {
        System.out.println("Start Normal process...");
    }

    @Override
    public void stopState(Car car) {
        System.out.println("Stop Normal process...");
    }
}
```

```java
public class TurboState implements BasicCarState{
    private static TurboState turboState;

    private TurboState(){}

    public static BasicCarState getInstance(){
        if(turboState==null){
            turboState= new TurboState();
        }
        return turboState;
    }
    @Override
    public void driveState(Car car) {
        System.out.println("Start Turbo process...");
    }

    @Override
    public void stopState(Car car) {
        System.out.println("Stop Turbo process...");
    }
}
```



이제 우리가 설계한 자동차 객체를 실험해보자.

```java
public class Test {
    public static void main(String[] args) {
        Car avante = new BasicCar();
        avante.drive();
        avante.stop();

        avante.setState(TurboState.getInstance());
        avante.drive();
        avante.stop();
    }
}

/*
Start Normal process...
Stop Normal process...
Start Turbo process...
Stop Turbo process...
*/
```

avante의 상태를 바꾸니 아반떼의 행동이 달라졌다.



> 상태 패턴과 전략 패턴의 차이점?

상태 패턴은 객체의 상태를 캡슐화해서 객체로 관리하는 패턴이다.
이 상태는 원래 객체의 다른 행동에 영항을 미친다.

전략 패턴은 객체의 특정 행동(특히 행동을 수행하는 방식이 달리질 가능성이 많은 행동)을
독립적으로 구성하는 패턴이다.

사실 실사용에서는 매우 비슷하게 보이지만, 
상태는 원래 객체의 다른 행동에도 큰 영향을 미치지만,
전략은 변하기 쉬운 행동을 독립적으로 구성하는 것이라고 파악하자.



## 더 나아가기

이번엔 활용 예시를 더 발전시켜보자.

자동차 객체에 연료 필드를 추가해 연료에 따라 자동차의 주행모드를 동적으로 변환시켜서 운행시켜보자.

먼저 자동차 추상 클래스를 만들어보자.

```java
public abstract class Car {
    public int fuel;
    public CarState carState;

    public abstract void drive();

    public void setCarState(CarState carState){
        this.carState = carState;
    }
}
```

자동차에 남은 연료를 표시하는 fuel필드가 추가됐고, setter 메서드를 구현해놨다.



이제 자동차의 다양한 주행 모드를 추상화하는 인터페이스를 만들어보자.

```java
public interface CarState {
    void driveState(Car car);
    void stopState(Car car);
}
```

주행모드로 주행하고 주행모드를 멈추는 역할 두가지를 수행한다.
이제 다양한 주행모드를 구현해서 만들어보자.

```java
public class NormalState implements CarState{
    final int fuelPerHour = 10;

    @Override
    public void driveState(Car car) {
        car.fuel-=fuelPerHour;
        System.out.println("Drive NormalState...");
    }

    @Override
    public void stopState(Car car) {
        System.out.println("Stop NormalState...");
    }
}
```

```java
public class TurboState implements CarState{
    final int fuelPerHour = 40;

    @Override
    public void driveState(Car car) {
        car.fuel-=fuelPerHour;
        System.out.println("Drive Turbo state...");
    }

    @Override
    public void stopState(Car car) {
        System.out.println("Stop Turbo state...");
    }
}
```

```java
public class EcoState implements CarState {
    final int fuelPerHour = 3;

    @Override
    public void driveState(Car car) {
        car.fuel-=fuelPerHour;
        System.out.println("Drive EcoState...");
    }

    @Override
    public void stopState(Car car) {
        System.out.println("Stop Eco State...");
    }
}
```

이 상태는 연료소비량을 나타내는 fuelPerHour를 상수로 가지고 있고,
각 메서드를 구현했다.



이제 자동차 추상 클래스를 상속받은 클래스를 만들어서,
drive 메소드를 구현해보자.

우리는 자동차 객체에 남은 연료량에 따라 동적으로 주행모드를 변화시키고 싶다.

```java
public class BasicCar extends Car{
    public BasicCar(){
        this.fuel = 100;
        this.carState = new NormalState();
    }
    @Override
    public void drive() {
        if(fuel<15&& ! (carState instanceof EcoState)){
            carState.stopState(this);
            this.setCarState(new EcoState());
        } else if(fuel>15 && fuel<80&& ! (carState instanceof NormalState)){
            carState.stopState(this);
            this.setCarState(new NormalState());
        } else if(fuel > 80 &&! (carState instanceof TurboState)){
            carState.stopState(this);
            this.setCarState(new TurboState());
        }
        carState.driveState(this);
    }
}
```



이제 마지막으로 테스트를 해보자.

```java
public class Test {
    public static void main(String[] args) {
        Car avante = new BasicCar();
        avante.setCarState(new TurboState());
        while(avante.fuel>0){
            avante.drive();
        }
    }
}
```

```java
//실행결과
Drive Turbo state...
Stop Turbo state...
Drive NormalState...
Drive NormalState...
Drive NormalState...
Drive NormalState...
Drive NormalState...
Stop NormalState...
Drive EcoState...
Drive EcoState...
Drive EcoState...
Drive EcoState...
```

실행결과를 보면 자연스럽게 자동차 주행 모드를 변화시키는 것을 알 수 있다.
