---
title: 동적 파라미터화 코드 전달하기
date: 2021-07-30 09:19:44
tags:
    - Modern Java in Action
category:
    - Java
---
_'녹색 사과를 모두 찾고 싶어요... 아니다! 150그람 이상인 사과를 모두 찾고 싶어요...._

_생각해보니 150그람 이상이면서 녹색인 사과를 모두 찾아야 될 것 같네요..'_

이렇게 시시각각 변화는 사용자의 요구 사항을 최소 비용으로 대응하기 위해서는 어떻게 해야할까?

### **a. 동적 파라미터화란…**

동적 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 말한다.

나중에 실행될 메서드의 인수로 코드 블록을 전달할 수 있다.

### **b. 변화하는 요구사항에 대응하기**

맨 처음 사과 요구사항을 자꾸 바꿔서 요청하는 예시를 해결해보자!

#### b-1. 첫번째 시도. 녹색 사과 필터링

```java
enum Color{RED, GREEN}

public static List<Apple> filterGreenApple(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>(); 
    for (Apple apple:inventory){
        if(GREEN.equals(apple.getColor()){
            result.add(apple);
        }
    }
    return result;
}
```

이 코드에서 만약 요구사항이 녹색 사과에서 빨간 사과를 수집하도록 바뀐다면 어떨까?

이럴 땐 이 규칙을 기억해야 한다.

거의 비슷한 코드가 반복 존재한다면 그 코드를 추상화한다.

#### b-2. 두번째 시도, 색을 파라미터화

```java
public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color){
    List<Apple> result = new ArrayList<>();
    for(Apple apple: inventory){
        if(apple.getColor().equals(color)){
            result.add(apple);
        }
    }
    return result;
}
```

이런 식으로 코드를 짜면, 코드를 반복하여야지 않고 다양한 색에 대한 요구사항을 효율적으로 대응할 수 있을 것이다.

```java
List<Apple> greenApples = filterApplesByColor(inventory, GREEN);
List<Apple> redApples = filterApplesByColor(inventory, RED);
```

하지만 사용자가 이번엔 색깔이 아닌 다른 요소(무게나 모양 등)로 요구사항을 제시한다면 어떻게 될까?

```java
public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight){
    List<Apple> result = new ArrayList<>();
    for (Apple apple: inventory){
        if(apple.getWeight()> weight){
            result.add(apple);
        }
    }
    return result;
}
```

이런 식으로 무게에 대한 새로운 메소드를 만들 수 있지만, 결국 색 필터링 코드와 대부분 중복된다..

이런 중복된 코드는 성능 개선하려 할 때 메서드 전체 구현을 고쳐야 하는 대참사가 발생할 수 있다..

#### b-3. 세번째 시도, 가능한 모든 속성으로 필터링

정말 해서는 안되는 방법이지만 이런 방법도 있긴하다.

```java
public static List<Apple> filterApples(List<Apple> inventory, Color color, int weight, boolean flag){
    List<Apple> result = new ArrayList<>();
    for(Apple apple: inventory){
        if((flag && apple.getColor().equals(color)) ||(!flag&&apple.getWeight()>weight)){
            result.add(apple);
        }
    }
    return result;
}

List<Apple> greenApples = filterApples(inventory, GREEN, 0, true);
List<Apple> heaveyApples = filterApples(inventory, null, 150, false);
```

정말 형편없는 코드다..

도대체 true와 false가 어떤 것을 의미하는 건가? 새로운 조건이 필요하면 어떻게 할 것인가?

### **c. 동적 파라미터화**

요구 조건들을 어떤 속성에 기초해서 불리언값을 반환하는 형태로 구현할 수 있다.

참 거짓을 반환하는 함수를 프레디케이트 라고 한다.

```java
public interface ApplePredicate {
    boolean test (Apple apple);
}

public class AppleHeavyWeightPredicate implements ApplePredicate {
    public boolean test(Apple apple) {
        return apple.getWeight()>150;
    }
}

public class AppleGreenColorPredicate implements ApplePredicate {
    public boolean test(Apple apple) {
        return GREEN.equals(apple.getColor());
    }
}
```

이와 같은 방식을 전략 디자인 패턴이라고 부르는데,

각 알고리즘(전략이라고 부른다)을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에, 런타임이 알고리즘을 선택하는 기법이다.

즉 메서드가 다양한 동작(또는 전략)을 받아서 내부적으로 다양한 동작을 수행 할 수 있다.

#### c-1. 네번째 시도, 추상적 조건으로 필터링

ApplePredicate 인터페이스를 사용하면 이런 식으로 구현할 수 있다.

```java
public static List<Apple> filterApple(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple: inventory) {
        if(p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}
```

훨씬 좋아졌다!!

이를 바탕으로 무게가 150 이상이고 빨간 사과를 모아 달라는 요구조건을 해결해보자!

```java
public class AppleRedAndHeavyPredicate implements ApplePredicate {
    public boolean test(Apple apple) {
        return RED.equals(apple.getColor())&& apple.getWeight()>150;
    }
}

List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedAndHeavyPredicate());
```

우리가 전달한 ApplePredicate 객체에 의해 filterApples 메서드의 동작이 결정된다!!!!

안타깝게도 메서드는 객체만 인수로 받으므로 test메서드를 ApplePredicate 객체로 감싸서 전달해야 했다.

### **d. 복잡한 과정 간소화**

위에서 구현한 방식, 즉 프레디케이트로 필터링 할 경우, 조건 마다 여러 클래스를 정의한 다음, 인스턴트화 해야 하므로 번거롭다.

이를 익명 클래스의 람다 표현식으로 해결해보자

#### d-1. 다섯번째 시도, 익명 클래스 사용

익명 클래스는 자바의 지역 클래스와 비슷한 개념이다.

익명 클래스는 클래스 선언과 인스턴스화를 동시에 한다.

익명 클래스를 활용한 ApplePredicate 객체를 생성하는 방식으로 필터링해보자

```java
List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple){
        return RED.equals(apple.getColor());
    }
});
```

filterApples 메서드의 동작을 익명클래스로 직접 파라미터화 했다.

하지만 익명 클래스는 여전히 문제가 많다.

일단 람다에 비해 차지하는 공간이 많고, 이해하기도 어렵다.

#### d-2. 여섯번째 시도, 람다 표현식 사용

람다 표현식을 사용하면 굉장히 간단하게 구현할 수 있다.

```java
List<Apple> result = filterApples(inventory, (Apple apple)-> RED.equals(apple.getColor()));
```

#### d-3. 일곱번째 시도, 리스트 형식으로 추상화

이제 사과 말고 다양한 개체들이 필터링 될 수 있도록 활용해보자

```java
public interface Predicate<T> {
    boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> result = new ArrayList<>();
    for(T e: list) {
        if(p.test(e)) {
            result.add(e);
        }
    }
    return result;
}

List<Apple> redApples = filter(inventory, (Apple apple)->RED.equals(apples.getColor());
List<Integer> evenNumbers = filter(numbers, (Integer I)->i%2==0);
```

람다 표현식으로 인터페이스를 보다 용이하게 구현했다!!!!

### **e. 실전 예제 **

#### e-1 Comparator로 정렬하기

List.sort나 Collections.sort에 java.util.Comparator 객체를 이용해서 sort의 동작을 파라미터화 해보자

```java
// java.util.Comparator
public interface Comparator<T> {
    int compare(T o1, T o2);
}

inventory.sort(new Comparator<Apple>() [
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
});

inventory.sort((Apple a1, Apple a2)->a1.getWeight().compareTO(a2.getWeight()));
```