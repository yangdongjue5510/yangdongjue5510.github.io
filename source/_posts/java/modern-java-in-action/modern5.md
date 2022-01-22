---
title: 다양한 형태의 스트림 만들기
date: 2021-07-30 09:39:50
tags:
    - Modern Java in Action
category:
    - Java

---
# 숫자형 스트림

reduce 메서드로 스트림 요소의 합을 구하는 사례를 보자

```java
int calories = menu.stream()
                   .map(Dish::getCalories)
                   .reduce(0, Integer::sum);
```

> 이런 경우, reduce에 박싱비용이 포함되어 있다. 합계를 계산하기 전에 Integer를 기본형으로 언박싱해야한다.

## 기본형 특화 스트림

자바에서는 이런 박싱 비용을 피할 수 있도록 세 가지 기본형 특화 스트림을 제공한다.

1.  int 특화 IntStream
2.  double 특화 DoubleStream
3.  long 특화 LongStream

> 이런 특화 스트림은 오직 박싱 과정에서 일어나는 효율성과 관련 있으며, 스트림에 추가 기능을 제공하지는 않는다.

### 숫자 스트림으로 매핑

스트림을 특화 스트림으로 변환할 때는 mapToInt, mapToDouble, mapToLong 세가지 메서드를 주로 사용한다.  
이 메서드들은 기존의 map과 동일한 기능이지만, Stream 대신 특화된 스트림을 반환한다.

```java
int calories = menu.stream()
                     .mapToInt(Dish::getCalories)
                     .sum();
```

mapToInt 메서드가 각 요리에서 모든 칼로리를 추출하고(Integer), IntStream을 반환한다.  
따라서 IntStream이 제공하는 sum메소드를 활용할 수 있다.

### 숫자 스트림을 객체 스트림으로 복원하기

숫자 스트림은 기본형의 값만 만들 수 있다. 만약 다른 값을 반환하려면 어떻게 해야 할까?  
boxed 메서드를 활용하면 특화 스트림을 일반 스트림으로 변환할 수 있다.

```java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed();
```

### 기본값 : OptionalInt

숫자 스트림에서 해당 조건에 만족하는 요소가 없는 경우와, 해당 조건에 맞는 요소가 0인 경우, 둘 다 0을 반환해서 혼동이 올 수 있다.  
그렇다면 이 두 가지 경우를 어떻게 해야 구분할 수 있을까?

바로 OptionalInt, OptionalDouble, OptionalLong으로 가능하다.

```java
OptionalInt maxCalories = menu.stream()
                              .mapToInt(Dish::getCalories)
                              .max();
```

이제 조건에 맞는 값이 없는 경우와 0이 반환되는 요소가 서로 구별 가능하므로 다음과 같은 기본값도 명시할 수 있다.

```java
int max = maxCalories.orElse(1);
```

### 숫자 범위 정하기

> 스트림에서 특정 범위의 숫자를 사용해야 되는 상황에서는 어떻게 해야 할까?

IntStream과 LongStream에서는 range와 rangeClosed 두가지 정적 메서드를 제공한다.  
두 메서드 모두 첫 인수는 시작값, 두번째 인수는 종료값을 갖는다.  
range는 시작값과 종료값이 범위에 포함되지 않고,  
rangeClosed는 시작값과 종료값이 범위에 포함된다.

```java
IntStream evenNumbers = IntStream.rangeClosed(1, 100)
                                 .filter(n->n%2==0);
System.out.println(evenNumbers.count());
```

이 예시는 1~100까지 한정한 상황에서 필터로 짝수만 찾아 출력한 코드이다.

# 스트림 만들기

컬렉션이나 숫자 뿐만 아니라 다양한 방식으로 스트림을 만들 수 있다.  
배열, 파일, 함수를 이용한 스트림을 알아보자

### 값으로 스트림 만들기

> 임의의 수들을 인수로 받는 정적 메서드 Stream.of를 이용해 스트림을 만들수 있다.

```java
Stream<String> stream = Stream.of("Modern", "Java", "Action");
stream.map(String::toUpperCase).forEach(System.out::println);
```

또한 empty 메서드로 스트림을 비우거나 빈 스트림을 만들 수 있다.

```java
Stream<String> emptyStream = Stream.empty();
```

### null이 될 수 있는 객체로 스트림 만들기

> null을 가질 수 있는 객체는 null일 경우를 검사해서 빈 스트림을 반환해야 한다.

기존의 방식으로 null을 검사하면 다음과 같다

```java
String homeValue = System.getProperty("home");
Stream<String> homeValueStream = homeValue == null ? Stream.empty() : Stream.of(value);
```

하지만 자바 9에서 지원하는 Stream.ofNullable을 이용해 다음과 같이 구현할 수 있다!

```java
Stream<String> homeValueStream = Stream.ofNullable(System.getProperty("home");
```

flatMap과 사용하면 다음과 같은 코드도 가능하다!

```java
Stream.of("config", "home", "user")
      .flatMap(key->Stream.ofNullable(System.getProperty(key)));
```

### 배열로 스트림 만들기

> 정적 메서드 Arrays.stream를 통해 숫자 스트림이나 일반 스트림을 만들어낼 수 있다.

```java
int[] numbers = {2,3,4,5,6};
int sum = Arrays.stream(numbers).sum();
```

### 파일로 스트림 만들기

> java.nio.file.Files 의 많은 정적 메서드가 스트림을 반환한다.

```java
long uniqueWords = 0;  
try(Stream lines = Files.lines(Paths.get("data.txt"), Charset.defaultCharset())){
    uniqueWords = lines.flatMap(line->Arrays.stream(line.split(" ")))
                       .distinct()
                       .count();
}
catch(IOException e){
    //예외처리
}
```

위 예시에서 File.lines로 파일의 각 행 요소를 반환하는 스트림을 얻는다.  
스트림 소스가 I/O 자원이므로, try-catch 블록으로 감싸고 finally 블록으로 자원을 닫아줘야 하지만.  
스트림 인터페이스는 AutoCloseable 인터페이스를 구현하므로 try 블록 자원을 자동으로 관리한다.  
각 행 요소를 split메서드를 통해 단어별 요소로 만들고,  
이 단어들을 distinct와 count를 연결해 고유 단어들의 갯수를 계산했다.

### 함수로 무한 스트림(언바운드 스트림) 만들기

> 크기가 고정되지 않은 스트림은 두 정적 메서드 Stream.iterate, Stream.generate로 구현한다.

iterate와 generate에서 만든 스트림은 요청때마다 주어진 함수로 값을 만든다.  
보통 무한한 스트림을 제한하기 위해 limit을 연결해 사용한다.

#### iterate 메서드

```java
Stream.iterate(0, n->n+2)
      .limit(10)
      .forEach(System.out::println);
```

iterate메소드는 초기값과 람다를 인수로 받아 무한히 실행한다.  
예제에서는 n->n+2이므로, 0, 2, 4, 6 .... 이런식으로 요소들을 만들어 낸다.  
limit으로 10개의 요소만 생성하고, 각 요소를 출력하며 스트림을 끝낸다.

자바 9의 iterate 메소드는 프레디케이트를 지원한다.

```java
IntStream.iterate(0, n->n<100, n->n+4)
         .forEach(System.out::println);
```

그런데 여기서 이런 의문이 생긴다.  
다음과 같은 코드는 작동할까?

```java
IntStream.itrerate(0, n->n+4)
         .filter(n->n<100)
         .forEach(System.out::println);
```

언뜻 보면 잘 작동할 것 같지만, _위 코드는 무한히 계산하여 종료되지 않는다!!!!!_

엥 왜?  
filter는 이 작업이 언제 끝내야되는지 모르기 때문이다.  
filter는 100이 넘어가도 계속 자신의 해야할 계산을 한다.

우리의 의도를 달성하려면 _스트림 쇼트서킷_을 지원하는 takeWhile을 사용해야 한다.

```java
IntStream.iterate(0, n->n+4)
         .takeWhile(n->n<100)
         .forEach(System.out::println);
```

#### generate 메서드

generate는 iterate와 다르게, 생산된 각 값을 연속적으로 계산하지 않는다.

generate는 Supplier<T>를 인수로 받아 새로운 값을 생산한다.

```java
Stream.generate(Math::random)
      .limit(5)
      .forEach(System.out::println);
```

이 코드는 0과 1 사이의 임의 더블 수 다섯개를 만든다.