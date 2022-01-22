---
title: 스트림 더 알아보기
date: 2021-07-30 09:37:30
tags:
    - Modern Java in Action
category:
    - Java

---
# a. 필터링

스트림 요소를 선택하는 방법을 필터링이라고 한다.  
프레디케이트 필터링과 고유 요소만 필터링하는 방법에 대해 알아보자!

## 프레디케이트로 필터링하기

프레디케이트를 인수로 받아 프레디케이트와 일치하는 모든 요소를 포함하는 스트림을 반환하는 방식을 말한다.  
대표적으로 filter method가 있다. 예시로 보자!

```java
List<Dish> vegetarianMenu = menu.stream()
                            .filter(Dish::isVegetarian)
                            .collect(toList());
```

## 고유 요소 필터링

고유 요소로 이뤄진 스트림을 반환하는 distinct method를 지원한다.  
여기서 고유 요소라는 말은 중복된 요소가 없는 요소를 의미한다.  
중복된 요소를 검증은 hashCode, equals로 결정된다.

```java
List<Integer> numbers = Arrays.asList(1,2,3,3,2,4);
numbers.stream()
        .flter(i -> i%2 ==0)
        .distinct()
        .forEach(System.out::println);//2,4
```

# b. 스트림 슬라이싱

스트림 요소를 선택하거나 스킵하는 여러 방법에 대해 알아보자.  
프레디케이트 이용하기, 스트림 초기 몇 요소 무시하기, 특정 크기로 스트림 줄이기 등 다양한 방법이 있다.

## b-1. 프레디케이트 :takeWhile 활용

스트림의 요소(음식)들이 이미 칼로리로 정렬되어 있을 때, 특정 칼로리보다 높은 칼로리를 갖는 음식들만 보고 싶으면 어떻게 해야 될까?  
이전에 배웠던 filter로 모든 요소를 확인해볼수 있지만, 요소들은 이미 칼로리로 정렬되어 있다.  
즉 특정 칼로리와 비교 연산의 결과가 달라지는 순간부터는 굳이 다음 요소를 검증하지 않아도 된다는 의미다.  
takeWhile은 처음 거짓이 나올 때까지 요소들을 모은다. 즉 거짓이 나오면 요소를 그만 모은다.  
이를 takeWhile연산으로 구현할 수 있다.

```java
List<Dish> sliceMenu1 = specialMenu.stream()
                                   .takeWhile(dish->dish.getCalories() < 320)
                                   .collect(toList());
```

## b-2. 프레디케이트 :dropWhile 활용

takeWhile과는 달리 dropWhile은 정반대이다. 조건에 거짓이 되는 요소가 나오게 되면 그 지점에서 작업을 중단하고, 남은 모든 요소를 반환한다.  
dropWhile은 무한한 남은 요소를 가진 무한 스트림에서도 동작한다.

```java
List<Dish> sliceMenu1
    = specialMenu.stream()
                 .takeWhile(dish->dish.getCalories()<320)
                 .collect(toList());
```

## b-3. 스트림 축소: limit

주어진 값 이하의 요소를 갖는 스트림을 반환하는 limit(n)을 활용하면 스트림을 줄일 수 있다.

```java
List<Dish> dishes = specialMenu.stream()
                               .filter(dish->dish.getCalories() > 300)
                               .limit(3)
                               .collect(toList());
```

처음 일치하는 n개의 요소만 반환한다.

## b-4. 요소 건너뛰기: skip

스트림으로 들어온 요소 n개를 제외한 스트림을 반환하는 skip(n) 메서드를 활용할 수 있다.

```java
List<Dish> dishes = menu.stream()
                        .filter(d -> d.getCalories() > 300)
                        .skip(2)
                        .collect(toList());
```

# c. 매핑

특정 객체에서 특정 데이터를 선택하는 map과 flatMap으로 매핑을 구현한다

## c-1. 스트림의 각 요소에 함수 적용하기: map

map은 함수를 인수로 받아 각 요소에 적용한 결과가 새로운 요소로 매핑된다.

```java
List<String> dishNames = menu.stream()
                             .map(Dish::getName)
                             .collect(toList());
```

## c-2. 스트림 평면화: flatMap

자 flatMap을 배우기 전에, 이게 왜 필요한지 알아보자.  
hello와 world라는 문자열 두개를 배열에 저장한 다음, 각 문자열의 고유문자만 모아 리스트에 넣어 반환하고 싶다고 하자.  
우리가 원하는 결과물은 h,e,l,o,w,r,d를 가진 리스트다.

flatMap을 활용하지 않고 하면 시도를 하면 이렇게 된다

```java
words.stream()
     .map(word->word.split(""))
     .distinct()
     .collect(toList());
```

근데 이렇게 하면 map에서 해당 함수를 적용해도 문자배열을 가진 두 객체가 요소가 되는거지, 하나하나의 문자가 스트림요소가 되는게 아니다!  
즉 {h,e,l,l,o}, {w,o,r,l,d} 이렇게 두 요소가 반환되는거지, h,e,l,l,o,w,o,r,l,d 이런 식으로 문자들이 다 요소가 되는 게 아니다.  
결국 이렇게 하면 distinct 메소드는 두 요소를 비교하게 되는데, 두 요소는 엄연히 다른 요소이므로, 두 문자열 둘다 살아남게 된다.  
이제 collect를 통해 문자열 두개를 포함한 리스트가 반환된다.

자 그러면 이제 flatMap을 활용해보자.  
일단 우리가 원하는 목표를 달성하기 위해서는 배열 스트림이 아닌 문자열 스트림이 필요하다.  
문자열을 받아 문자열 스트림으로 활용하려면, Arrays.stream() 메서드를 활용하면 된다.

```java
String[] arrayOfWords = {"hello", "world"};
Stream<String> streamOfWords = Arrays.stream(arrayOfWords);
```

이런 파이프라인으로 하면 문자열 스트림이 생성된다.  
이 파이프라인으로 우리가 원래 하려던 것을 해보자

```java
words.stream()
     .map(word ->word.split(""))
     .map(Arrays::stream) //각 배열을 별도의 스트림으로 생성
     .distinct()
     .collect(toList());
```

하지만 아직 문제가 있다. .map(Arrays::stream)을 하면, 각 문자열 배열들이 개별의 스트림이 되어  
toList() 반환값이 List이 되어버린다. 즉 스트림을 가진 리스트가 되어버린다. 우리가 원하는건 문자열은 가진 리스트다.

이를 위해 flatMap을 도입하자. flatMap 각 배열을 스트림이 아닌 한 스트림의 컨텐츠로 매핑한다.

```java
List<String> uniqueCharacters =
    words.stream()
         .map(word->word.split(""))
         .flatMap(Arrays::stream)
         .distinct()
         .collect(toList());
```

# d. 검색과 매칭

## d-1. anyMatch 프레디케이트가 적어도 한 요소와 일치하는지 확인

```java
if(menu.stream().anyMatch(Dish::isVegetarian)){
    System.out.println("the menu is vegetarian friendly!");
}
```

## d-2. allMatch 프레디케이트가 모든 요소와 일치하는지 검사

```java
boolean isHealthy = menu.stream()
                        .allMatch(dish -> dish.getCalories()<1000);
```

## d-3. noneMatch 프레디케이트와 일치하는 요소가 없는지 검사

allMatch와 반대 되는 메서드라고 생각하자.

```java
boolean isHealthy = menu.stream().noneMatch(d->d.getCalories() >= 1000);
```

> 이렇게 anyMatch, allMatch, noneMatch, findAny는 논리연산자 &&,||와 비슷하게 전체 스트림을 처리하지 않았더라도 결과를 반환할 수 있다.

### 이를 쇼트 서킷이라 부른다!(특히 무한한 요소를 가진 스트림을 유한 요소 스트림으로 줄일 때 유용하다!)(limit도 포함!)

## d-4. findAny 요소 검색해서 해당 요소 반환

```java
Optional<Dish> dish = menu.stream()
                          .filter(Dish::isVegetarian)
                          .findAny();
```

여기서 optional은 추후에 더 공부할 것이니 넘어가고, 지금은 조건에 맞는 요소들을 찾아 반환해준다는 사실만 확인하고 넘어가자.

## d-5. findFirst 논리적인 순서를 가진 스트림에서 첫번째 요소 찾기

```java
List<Integer> someNumbers = Arrays.asList(1,2,3,4,5);
Optional<Integer> firstSquareDivisibleByThree = someNumbers.stream()
                                                           .map(n->n*n)
                                                           .filter(n->n%3 ==0)
                                                           .findFirst(); //9
```

> findFirst와 findAny는 언제 사용하나?

병렬 실행에서는 첫번째 요소를 찾기가 어렵다! 그럴 때 findFirst를 사용하고 그렇지 않으면, findAny를 사용한다!!

# e. 리듀싱

리듀스 연산은 모든 스트림 요소를 처리해서 값으로 도출하는 연산이다.  
함수형 프로그래밍 언어로는 폴드라고 부른다.

## d-1. reduce 요소의 합

요소의 합을 구하는 reduce에는 일반적으로 두 개의 파라미터가 필요하다.  
하나는 누적값의 초기값, 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator (보통 람다로 표현).

```java
int product = numbers.stream().reduce(1, (a,b)->a*b);
```

위 코드는 1부터 시작해서, 1+첫요소가 다시 누적값이 되고 (1+첫요소)+두번째요소가 되는 식이다.(물론 얘도 다음 요소와 연산될 때는 누적값이 된다.)

> 초기값을 받지 않는 reduce도 있다. Optional을 반환한다.

```java
Optional<Integer> sum = numbers.stream().reduce((a, b) -> (a+b));
```

## d-2. reduce로 최댓값 최솟값 찾기

최대 최소를 구할 때는 reduce의 초기값(누적)이 필요가 없다. 그저 요소를 순서대로 받아 max연산을 하면 된다.

```java
Optional<Integer> max = numbers.stream().reduce(Integer::max);
Optional<Integer> min = numbers.stream().reduce(Integer::min);
```

> reduce 메서드의 장점과 병렬화

반복으로 합계를 구하는 것과 reduce의 차이는 뭘까?  
reduce는 스트림이므로, 내부 구현으로 병렬로 실행할 수 있다.

## 스트림 연산 : 상태 없음과 상태 있음

map, filter는 각 요소를 하나씩 받아 결과를 출력 스트림으로 보내는 내부 상태를 갖지 않는 연산이다.  
한편 reduce, sum, max 같은 연산은 결과를 누적할 내부 상태가 필요하다. 스트림의 요소 수와 관계없이 내부 상태의 크기는 한정적이다.  
하지만 sorted, distinct는 요소를 하나하나 받아 결과를 출력 스트림으로 보내는 연산이 아니라, 모든 요소를 버퍼에 추가하여 결과를 출력 스트림으로 보내는 연산이다.  
이런 연산들은 무한 스트림을 입력받으면 안된다. 예를 들어 모든 소수를 담은 무한 스트림을 sorted 할 수 있을까? 세상에서 가장 큰 소수가 맨 앞으로 와야되는데, 그런 연산은 불가능하다!. 이런 연산들을 우리는 내부 상태를 갖는 연산이라고 한다.