---
title: 5. SOLID 원칙
date: 2021-09-30 13:23:53
tags:
category:
    - Java
    - Design Pattern
---
## SOLID
확장과 유지보수를 쉽게 만드는 객체지향 프로그래밍 설계의 다섯가지 원칙.


### S : 단일 책임 원칙(SRP)
**한 설계 부품(클래스, 함수..)는 하나의 책임만 가져야 한다.**
*약은 약사에게 진료는 의사에게...*
한 부품에 여러 기능이 있으면, 내부 메소드들이 의지하는 부분이 많아진다.
즉 결합도가 높아지고, 그 부품에 대한 책임 높아진다. -> 유지보수가 힘들어진다.
```java
class ICanDoEverything{
    public void plus(int a, int b){
        System.out.println(a+b);
    };
    public void display(Displayable file){file.play();}
    public void singASong(Singable singer){singer.sing();}
}
public class SRPTest {
    public static void main(String[] args) {
        ICanDoEverything hero = new ICanDoEverything();
        Displayable file = new Jpg();
        Singable singer = new RealSinger();
        int a1 = 1; int a2 =2;
        hero.display(file); //책임 1
        hero.singASong(singer); //책임 2
        hero.plus(a1, a2);//책임 3
    }
}
```
숫자 두개를 더하고, 파일을 받아 실행하고, 가수를 불러 노래도 시킬 수 있는 ICanDoEverything 클래스가 있다!
이 클래스의 객체 hero는 main메서드에서 3가지 책임을 하고 있다.
근데 만약 이 클래스의 한 메서드에서 문제가 생기면 어떨까?
객체 hero의 3가지 책임이 모두 실행할 수 없게 된다!!!(그 책임이 문제와 큰 관련이 없어도 말이다)


### O : 개방-폐쇄 원칙(OCP)
**기존의 코드를 변경하지 않고(close) 기능을 수정하거나 추가 할 수 있어야 한다.(open)**
자주 변경되는 내용은 따로 분리하고, 자주 변경되지 않는 부분은 다른 부분이 변경 되더라도 영향을 덜 받아야 한다.
```java
package SOLID;

interface Displayable {
    public void play();
}
class Jpg implements Displayable{
    @Override
    public void play(){
        System.out.println("jpg displayed");
    }
}
class Gif implements Displayable{
    @Override
    public void play(){
        System.out.println("gif displayed");
    }
}
class FileDisplayer{
    Displayable file;
    public void setFile(Displayable file){
        this.file = file;
    }
    public void play(){
        file.play();
    }
}
public class ImageDisplay {

    public static void main(String[] args) {
        FileDisplayer fileDisplayer = new FileDisplayer();
        Displayable file = new Jpg();
        fileDisplayer.setFile(file);
        fileDisplayer.play();

        file = new Gif();
        fileDisplayer.setFile(file);
        fileDisplayer.play();
    }
}
```
여기서 변화할 내용은 play메소드이다.
인터페이스로 play 메소드를 인터페이스로 추상화했다.


### L : 리스코프 치환 원칙
**자식 클래스는 부모 클래스에서 가능한 행위를 수행할 수 있어야 한다.**
즉, 부모의 자리에 자식이 와도 문제 없어야 한다!
또한 자식이 부모의 기능을 재정의 하지 않고 확장하는 것이 좋다.
```java
class Account {
    int accountNum = 123456;
    String owner= "yang";
    int balance = 1000;
    int getBalance(){
        return this.balance;
    }
}
class SavingAccount extends Account{
    int interestRate = 2;
    int getBalance(){
        return this.balance * this.interestRate;
    }
}

class Bank{
    Account account = new Account();
    account.getBalance(); //1000반환

    account = new SavingAccount();
    account.getBalance(); //2000반환!
}
```
자식 클래스가 메소드를 재정의해서 같은 코드더라도 다른 결과를 일으키는 문제가 있다.

### I : 인터페이스 분리 원칙
**자신이 사용하지 않는 인터페이스는 구현하지 말아야 한다.(영향 받지 않아야 한다.)**
**하나의 큰 인터페이스보다 구체적인 여러 인터페이스가 낫다.**
```java
interface Everything{
    void run();
    void sing();
    void swim();
}
class Singer implements Everything{
    @Override
    public void run() {
        System.out.println("Singer can run?");
    }

    @Override
    public void sing() {
        System.out.println("Singer can sing a song!!");
    }

    @Override
    public void swim() {
        System.out.println("Singer can swim....?");
    }
}
```
노래를 하는 Singer클래스는 여러 기능을 포함하는 큰 인터페이스를 구현하고 있다.
이에 대한 부작용으로 Singer클래스는 자신이 해야할 역할 외에도 다른 기능을 구현해야 하게 된다.

```java
interface Singable{
    void sing();
}
interface Runnable{
    void run();
}
interface Swimable{
    void swim();
}
class RealSinger implements Singable{
    @Override
    public void sing() {
        System.out.println("Singer can sing a song~!");
    }
}
```
인터페이스를 구체적으로 나눴더니, Singer클래스가 필요한 역할만 구현할 수 있게 됐다!


### D : 의존 역전 원칙(DIP)
**의존 관계를 맺을 때, 변하기 쉬운 것보다 변화하기 어려운 것에 의존해라!**
변화하기 쉬운 것 -> 구체적으로 구현된 것
변화하기 어려운 것 -> 추상화 된 것(인터페이스, 추상 클래스)


**즉 의존관계를 맺을 때, 실제 구현된 클래스가 아닌 인터페이스나 추상 클래스과 맺으라는 뜻!!**

> 역전?

즉 원래 변하기 쉬운 것에 상위 모듈이 의존하면 : 변화할때마다 상위 모듈도 따라 변화해야 함!
그러나 인터페이스 같이 변하기 어려운 것에 상위 모듈이 의존하면?
하위 모듈은 인터페이스에 의존하도록 하여(역전!), 
수정을 아무리 해도 상위 모듈이 인터페이스를 의존하는 한 문제는 없다!


```java
public class ImageDisplay {

    public static void main(String[] args) {
        FileDisplayer fileDisplayer = new FileDisplayer();
        Displayable file = new Jpg();
        fileDisplayer.setFile(file);
        fileDisplayer.play();

        file = new Gif();
        fileDisplayer.setFile(file);
        fileDisplayer.play();
    }
}
```
아까 사용했던 예시에서 보면, 실제 구현된 Jpg 클래스의 메소드를 부르는게 아닌, 
Diplayable 인터페이스의 메소드를 호출하는 방식으로 의존관계를 만든다!!