---
title: 스트림에 대해 간략하게 알아보자
date: 2021-07-30 09:36:18
tags:
    - Modern Java in Action
category:
    - Java

---
## **a. 스트림이 뭘까?**

스트림은 자바 8 API에 새로 추가된 기능이다.

스트림을 이용하면 선언형(데이터를 처리하는 임시 구현 코드 대신 질의로 표현이 가능)으로 컬렉션 데이터를 처리할 수 있다.

쉽게 말하면, 스트림은 데이터 컬렉션 반복을 멋지게 처리하는 기능이라고 생각하자.

또한 스트림을 사용하면 멀티스레드 코드를 쓰지 않고도 투명하게 데이터를 병럴로 처리할 수 있다.(추후 알아보자)

일단 코드로 이해해보자

먼저 요리 중 저칼로리 요리명을 반환하고, 칼로리 기준으로 요리를 정렬하는 자바 7 코드를 보자.

```java
//요소 필터링
List<Dish> lowCaloricDishes = new ArrayList<>();
for (Dish dish : menu) {
    if(dish.getCalories() < 400) {
        lowCaloricDishes.add(dish);
    }
}
//요소 정렬
Collections.sort(lowCaloricDishes, new Comparator<Dish>() { //익명 클래스
    public int compare(Dish dish1, Dish dish2) {
        return Integer.compare(dish1.getCalories(), dish2.getCalories());
    }
}
//요소 이름을 저장
List<String> lowCaloricDishesName = new ArrayList<>();
for(Dish dish : lowCaloricDishes) {
    lowCaloricDishesName.add(dish.getName());
}
```

이 코드를 보면 요소 정렬하면서 lowCaloricDishes라는 가비지 변수를 사용했다.

lowCaloricDishes는 그저 정렬된 요소를 임시로 저장했다가 새로운 리스트인 lowCaloricDishesName에 전달해주는 역할을 한다.

자바 8에서는 이런 컨테이너 역할을 하는 중간 변수 구현을 라이브러리 내에서 모두 처리한다.

```java
import static java.util.Comparator.comparing;
import static java.util.stream.Collections.toList;
List<String> lowCaloricDishesName = menu.stream()
                                        .filtered(d -> d.getCalories() < 400)
                                        .sorted(Comparing(Dish::getCalories))
                                        .map(Dish::getName)
                                        .collect(toList());
```

_stream()을 parallelStream()으로 바꾸면, 멀티코어 아키텍처에서 병렬로 실행할 수 있다.(추후 게시물로 공부하자)_

그렇다면 스트림을 사용하면 어떤게 좋을까?

1.  선언형으로 코드를 구현할 수 있다.  
    즉, 반복문이나 조건문같은 제어블록을 사용해서 상황에 따라 어떻게 행동할 지 구현할 필요없이, 동작의 수행을 지정할 수 있다.  
    선언형 코드와 동작 파라미터화를 활용하면 요구사항에 쉽게 대응할 수 있다.(람다만 적절하게 바꿔주면 된다!)
2.  여러 빌딩 블록 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있다.  
    위 예시에서처럼 filtered, sorted, map, collect등 연결해 더 복잡한 파이프라인을 만들 수 있다.
3.  고수준 빌딩 블록(filter, sorted, map, collect 등..)은 특정 스레딩 모델에 제한되지 않고 사용가능 하다.  
    이 블록들은 멀티코어 아키텍처를 최대한 투명하게 활용할 수 있게 구현되어 있다. 덕분에 데이터 처리 과정을 병렬화하면서 스레드와 락을 걱정할 필요가 없다.

마지막으로 스트림의 특징을 요약하면서 다음 챕터로 넘어가자!

1.  선언형 : 더 간결, 가독성 상승
2.  조립 가능 : 유연성
3.  병렬화 : 성능 향상

## **b. 스트림 시작하기**

스트림에 대해 공부하기 앞서, 스트림이 정확하게 어떤 것을 의미하는 지 살펴보고 가자.

스트림이란 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소'를 의미한다.

무슨 말인지 아직도 모르겠다... 정의에 나온 단어 하나하나 파악해보자

-   연속된 요소?  
    컬렉션과 마찬가지로 스트림은 특정 요소 형식으로 이뤄진 연속된 값 집합의 인터페이스를 제공한다.  
    컬렉션은 요소 저장 및 접근 연산이 주를 이루지만, 스트림은 요소들의 표현이나 계산식이 주를 이룬다.
-   소스?  
    스트림은 컬렉션, 배열, 입출력 자원 등 데이터 제공 소스로부터 데이터를 소비한다.  
    컬렉션에서 요소들을 스트림에 연결하면, 컬렉션에 저장된 순서대로 스트림 요소가 소비된다는 의미!
-   데이터 처리 연산?  
    스트림은 함수형 프로그래밍 언어나 데이터베이스와 비슷한 연산을 지원한다.(filter, map, reduce, find, match, sort...)

스트림의 정의는 이렇게 이해할 수 있게 됐다.

한편 스트림의 두 가지 중요한 특징도 짚고 넘어가자!

-   파이프라이닝  
    대부분의 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록, 스트림 자신을 반환한다!
-   내부 반복  
    반복자를 이용해서 명시적으로 반복하는 컬렉션과는 달리 스트림은 내부 반복을 지원한다.

지금까지 배운 내용을 코드로 이해해 보자

```java
import static java.util.stream.Collectors.toList;
List<String> threeHighCaloricDishNames =
    menu.stream() //List<Dish> menu에서 스트림얻음
        .filter(dish -> dish.getCalories() > 300) //고칼로리 필터링
        .map(Dish::getName) //필터링 된 요리를 이름을 추출
        .limit(3) //선착순 세개만 저장
        .collect(toList()); //결과를 다른 리스트로 저장
System.out.println(threeHighCaloricDishNames);
```

이 코드를 도표로 나타내면 다음과 같다

[##_Image|kage@bhCDUs/btq9TEvcCqe/zfB1YOZVyffzyU46TnHLVK/img.png|alignCenter|data-origin-width="715" data-origin-height="506" width="666" height="471" data-ke-mobilestyle="widthOrigin"|||_##]

## **c. 스트림과 컬렉션**

자 이제, 스트림과 컬렉션을 비교해보자.

앞서 살펴본 것처럼 스트림과 컬렉션은 모두 연속된 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공한다.

연속된 이란 의미는 아무렇게나 값에 접근할 수 있는 것이 아닌 순차적으로 값에 접근한다는 의미다.

스트림과 컬렉션을 DVD와 비디오스트리밍의 관계로 이해할 수 있다.

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style4"><tbody><tr><td style="width: 50%;">DVD(컬렉션)</td><td style="width: 50%;">비디오스트리밍(스트림)</td></tr><tr><td style="width: 50%;">전체 자료구조가 저장되어 있음</td><td style="width: 50%;">전체가 저장되어 있지않고 사용할 일부만 받아와 사용</td></tr></tbody></table>

### c-1. 데이터를 언제 계산하는가

컬렉션과 스트림의 가장 큰 차이를 보이는 게 바로 데이터를 언제 계산하는 지이다.

컬렉션은 모든 데이터를 메모리에 저장하는 자료구조다.  
즉 컬렉션에 어떤 값을 추가하거나, 삭제하려 할 때마다 컬렉션의 모든 값이 메모리에 저장되어야하고,

추가하려는 값은 미리 계산되어야 한다.

스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조다.

고정됐다는 의미는 요소를 삭제하거나 제거할 수 없다는 의미다.

사용자가 요청하는 값만 스트림에서 추출하는 것이 핵심이다.

구글검색을 한다고 가정했을 때

모든 검색 결과를 가져올 때까지 기다리는 방식이 컬렉션이고,

가장 연관있는 10개를 먼저 보여주고 그 다음 페이지로 넘어가면 그 때 다음 10개를 가져오는 방식이 스트림이라고 이해하자!

### c-2. 딱 한 번만 탐색할 수 있다.

반복자(iterator)와 마찬가지로 스트림도 단 한번만 탐색할 수 있다.

어떤 요소가 한 스트림에서 이미 탐색됐을 때, 그 요소에 다시 접근하려면 새로운 스트림으로 접근해야 한다는 의미다.

(물론 데이터 소스도 반복 접근이 가능해야 한다. 입출력 소스 처럼 반복 사용할 수 없는 경우 스트림을 새로 만들어도 접근 불가능이다.)

### c-3. 외부 반복, 내부 반복

컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복해야 한다.(반복문을 써서)

이를 외부 반복이라고 한다.

하지만 스트림은 내부 반복을 사용한다.

내부 반복은 반복을 알아서 처리하고, 결과 스트림 값을 어딘가에 저장하는 방식이다.

함수에 동작만 결정하면 반복과 저장은 알아서 해준다는 의미다.

```java
List<String> names = new ArrayList<>();
for(Dish dish: menu) {
    names.add(dish.getName());
}

List<String> names = menu.stream()
                     .map(Dish::getName)
                     .collect(toList());
```

#### 내부 반복이 외부 반복보다 편리한 이유는 무엇일까?

방에 쓰레기를 주우라고 명령할 때 외부 반복은 마치

쓰레기가 있니? - 왼쪽에 쓰레기가 있네요- 주우렴 - 다시 쓰레기가 아직도 있니 - 오른쪽에도 쓰레기가 있네요 - 주우렴- ....

이런식인데, 내부 반복은

방에 있는 쓰레기를 모두 주우렴 - 주웠어요.

이렇게 실행된다.

또한 내부 반복은 쓰레기를 두 손으로 동시에 주울 수도 있고(투명한 병렬 처리), 쓰레기통을 가끼이 두고 빨리 처리 할 수 있다.(최적화)

내부반복과 외부반복은 라이브러리가 반복을 자기 안에서 해결하느냐 마느냐로 구분한다고 이해하자.

하지만 스트림의 내부 반복은 반복을 숨겨주는 연산 리스트가 미리 정의되어 있어야 한다.(filter, map..)

## **d. 스트림 연산**

스트림 연산은 크게 중간 연산, 최종 연산 두 가지로 나누어진다.

### d-1. 중간 연산

중간 연산은 다른 스트림을 반환하는 연산이다. (filter, map...)

중간 연산의 가장 중요한 특징은 최종 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다는 것이다.

중간 연산이 연결되어 합쳐지고, 이 것을 최종 연상으로 한번에 처리한다.

<table style="border-collapse: collapse; width: 100%; height: 114px;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 20%; height: 20px;">연산</td><td style="width: 20%; height: 20px;">형식</td><td style="width: 20%; height: 20px;">반환 형식</td><td style="width: 20%; height: 20px;">연산의 인수</td><td style="width: 20%; height: 20px;">함수 디스크립터</td></tr><tr style="height: 20px;"><td style="width: 20%; height: 20px;">filter</td><td style="width: 20%; height: 20px;">중간</td><td style="width: 20%; height: 20px;">Stream&lt;T&gt;</td><td style="width: 20%; height: 20px;">Predicate&lt;T&gt;</td><td style="width: 20%; height: 20px;">T -&gt; boolean</td></tr><tr style="height: 20px;"><td style="width: 20%; height: 20px;">map</td><td style="width: 20%; height: 20px;">중간</td><td style="width: 20%; height: 20px;">Stream&lt;R&gt;</td><td style="width: 20%; height: 20px;">Function&lt;T, R&gt;</td><td style="width: 20%; height: 20px;">T -&gt; R</td></tr><tr style="height: 18px;"><td style="width: 20%; height: 18px;">limit</td><td style="width: 20%; height: 18px;">중간</td><td style="width: 20%; height: 18px;">Stream&lt;T&gt;</td><td style="width: 20%; height: 18px;">&nbsp;</td><td style="width: 20%; height: 18px;">&nbsp;</td></tr><tr style="height: 18px;"><td style="width: 20%; height: 18px;">sorted</td><td style="width: 20%; height: 18px;">중간</td><td style="width: 20%; height: 18px;">Stream&lt;T&gt;</td><td style="width: 20%; height: 18px;">Comparator&lt;T&gt;</td><td style="width: 20%; height: 18px;">(T, T) -&gt; int</td></tr><tr style="height: 18px;"><td style="width: 20%; height: 18px;">distinct</td><td style="width: 20%; height: 18px;">중간</td><td style="width: 20%; height: 18px;">Stream&lt;T&gt;</td><td style="width: 20%; height: 18px;">&nbsp;</td><td style="width: 20%; height: 18px;">&nbsp;</td></tr></tbody></table>

### d-2. 최종 연산

최종 연산은 스트림 파이프라인에서 결과를 도출한다. 보통 최종 연산에서는 스트림이 아닌 List, Integer, void 등 도출된다.

<table style="border-collapse: collapse; width: 100%; height: 98px;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr style="height: 20px;"><td style="width: 20.4651%; height: 20px;">연산</td><td style="width: 12.907%; height: 20px;">형식</td><td style="width: 15.5814%; height: 20px;">반환 형식</td><td style="width: 51.0465%; height: 20px;">목적</td></tr><tr style="height: 40px;"><td style="width: 20.4651%; height: 40px;">forEach</td><td style="width: 12.907%; height: 40px;">최종</td><td style="width: 15.5814%; height: 40px;">void</td><td style="width: 51.0465%; height: 40px;">스트림 각 요소를 람다를 적용하면서 소비</td></tr><tr style="height: 20px;"><td style="width: 20.4651%; height: 20px;">count</td><td style="width: 12.907%; height: 20px;">최종</td><td style="width: 15.5814%; height: 20px;">long</td><td style="width: 51.0465%; height: 20px;">스트림 요소 갯수 반환</td></tr><tr style="height: 18px;"><td style="width: 20.4651%; height: 18px;">collect</td><td style="width: 12.907%; height: 18px;">최종</td><td style="width: 15.5814%; height: 18px;">collection</td><td style="width: 51.0465%; height: 18px;">스트림을 리스트, 맵, 정수 형식 컬렉션으로 만든다</td></tr></tbody></table>

#### 스트림 이용 과정은 다음 세가지로 요약 가능하다.

-   질의를 수행할 데이터 소스(컬렉션, 배열, 입출력 소스)
-   스트림 파이프라인을 구성할 중간 연산 연결
-   스트림 파이프라인을 실행하고 결과를 만들 최종 연산