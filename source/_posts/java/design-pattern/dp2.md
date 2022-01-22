---
title: 2. 옵저버 패턴
date: 2021-08-13 21:01:13
tags:
category:
    - Java
    - Design Pattern
---
## 옵저버 패턴
하나의 객체(서브젝트)가 변할 때, 자동으로 다른 여러 객체들(오브젝트)에게 영향을 동적으로 반영하는 일대다 관계 패턴
이때 여러 오브젝트가 추가 될 수 있어야 한다.


> 배울 내용 요약

### 1. 서로 상호작용하는 객체들은 느슨하게 연결되어야 한다.


> 예제로 알아보자.


NBA사무국에서는 선수들의 정보를 관리하고 있다.
NBA방송국 NBC, TNT에서는 이 정보를 받아 방송하고 있다.


선수들의 정보가 변하게 될 때, 이 두 방송국에서 자동으로 정보를 바꾸고, 바뀐 정보를 방송하는 프로그램을 만들어보자.


일단 방송국들이 반영할 사무국에 대한 인터페이스를 만들어보자
```java
package Observer;

public interface Subject {
    public void registerObserver(Observer o);
    public void removeObserver(Observer o);
    public void notifyObservers();
}
```


방송국들의 인터페이스도 만들어주자.
```java
package Observer;

public interface Observer {
    public void update(float ppg, float rpg, float apg);
}
```


사무국의 인터페이스를 기반으로 사무국 클래스를 만들어보자.
```java
package Observer;

import java.util.ArrayList;

public class NBAData implements Subject{
    private ArrayList observers;
    private float ppg;
    private float rpg;
    private float apg;

    public NBAData(){
        observers = new ArrayList();
    }

    public void registerObserver(Observer o){
        observers.add(o);
    }
    public void removeObserver(Observer o){
        int i = observers.indexOf(o);
        if (i >= 0){
            observers.remove(i);
        }
    }
    public void notifyObservers() {
        for (int i = 0; i < observers.size(); i++){
            Observer observer = (Observer)observers.get(i);
            observer.update(ppg, rpg, apg);
        }
    }
    public void measurementChanged(){
        notifyObservers();
    }
    public void setMeasurements(float ppg, float rpg, float apg){
        this.ppg = ppg;
        this.apg = apg;
        this.rpg = rpg;
        measurementChanged();
    }
}
```


#### 자, 이제 사무국은 값을 변경할 수 있고, arrayList에 옵저버(방송사)들을 추가하거나 제거하여, 자신의 변경사항을 방송국에게 전달해줄 수 있다.


이번엔 방송국 클래스를 만들어보자.
```java
package Observer;

public class NBCDisplay implements Observer, DisplayElement{
    private float ppg;
    private float rpg;
    private float apg;
    private Subject nbaData;

    public NBCDisplay(Subject nbaData){
        this.nbaData = nbaData;
        nbaData.registerObserver(this);
    }

    public void update(float ppg, float rpg, float apg){
        this.ppg = ppg;
        this.apg = apg;
        this.rpg = rpg;
        display();
    }

    public void display() {
        System.out.printf("This is from NBC!");
        System.out.printf(ppg+"points "+rpg+"rebounds "+apg+"assists!!!!\n");
    }
}

package Observer;

public class TNTDisplay implements Observer, DisplayElement{
    private float ppg;
    private float apg;
    private float rpg;
    private Subject nbaData;

    public TNTDisplay(Subject nbaData){
        this.nbaData = nbaData;
        nbaData.registerObserver(this);
    }

    @Override
    public void display() {
        System.out.printf("This is from TNT!");
        System.out.printf(ppg+"points "+rpg+"rebounds "+apg+"assists~!\n");
    }

    @Override
    public void update(float ppg, float rpg, float apg) {
        this.ppg = ppg;
        this.apg = apg;
        this.rpg = rpg;
        display();
    }
}
```


마지막으로 우리가 만든 녀석들이 잘 작동하는지 확인해보자!
```java
package Observer;

public class NbaBroadcast {
    public static void main(String[] args) {
        NBAData nbaData = new NBAData();

        TNTDisplay tntDisplay = new TNTDisplay(nbaData);
        NBCDisplay currentConditionsDisplay = new NBCDisplay(nbaData);

        nbaData.setMeasurements(10.2f, 5.23f, 3.3f);
        nbaData.setMeasurements(20.4f, 12.4f, 7.4f);
    }
}
```


> 배운 내용 확인하기

#### 1. 서로 상호작용하는 객체들은 느슨하게 연결되어야 한다.
우리는 인터페이스를 통해 옵저버와 서브젝트를 구현했다. 이들은 서로가 어떻게 구현됐는지 관심없다.
그냥 서로가 약속한 인터페이스를 구현하면 된다.