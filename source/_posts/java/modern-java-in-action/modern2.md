---
title: 람다표현식
date: 2021-07-30 09:25:25
tags:
    - Modern Java in Action
category:
    - Java
---
## **a. 람다란 무엇인가?**

람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이다!

-   익명 : 람다 표현식에는 이름이 없다.
-   함수 : 람다는 메서드와 달리 특정 클래스에 종속되지 않으므로 함수라 부른다.
-   전달 : 메서드 인수로 전달하거나 변수로 저장할 수 있다.
-   간결성 : 익명 클래스보다 간결하다.

커스텀 Comparator 객체 구현 예시로 람다의 힘을 느껴보지!

```java
Comparator<Apple> byWeight = new Comparator<Apple> {
    public int compare(Apple a1, Apple a2) {
        return a1.getWeight().compareTo(a2.getWeight());
    }
}

//Ramda
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

람다는 크게 세 부분으로 이뤄져있다.

-   파라미터 리스트 : (Apple a1, Apple a2)
-   화살표(->) : 람다의 파라미터 리스트와 바디를 구분
-   람다 바디 : a1.getWeight().compareTo(a2.getWeight()); 람다의 반환같이 해당하는 표현식.

람다는 표현식 스타일과 블록 스타일로 나타낼 수 있다.

-   표현식 스타일  
    (parameters) -> expression
-   블록 스타일  
    (parameters)-> { statements; }

구문(statements)와 표현식(expression)을 잘 구분해서 맞는 스타일대로 작성하기로 바란다!

아래 다양한 람다 사례를 보며 람다 표현식을 익혀보자!

```java
(List<String> list)-> list.isEmpty()
()->new Apple(10)
(Apple a)-> {
    System.out.println(a.getWeight());
}
(String s)-> s.length()
(int a, int b) -> a*b
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight())
```

## **b. 어디에 어떻게 람다를 사용할까?**

### b-1. 함수형 인터페이스

함수형 인터페이슨느 정확히 하나의 추상 메서드를 지정하는 인터페이스다.

대표적으로 Predicate, Comparator, Runnable 등이 있다.

(d 항목에서 더 자세히 다뤄본다.)

```java
public interface Predicate<T> {
    boolean test (T t);
}
public interface Comparator<T> {
    int compare(T o1, T o2);
}
```

_#인터페이스는 디폴트 메서드를 포함할 수 있다. 디폴트 메서드를 가지고 있더라도 추상메서드가 오직 하나면 함수형 인터페이스다._

함수형 인터페이스로 뭘 할 수 있을까?

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있다.

즉 전체 표현식을 함수형 인터페이스를 구현한 클래스의 인스턴스로 취급할 수 있다!!

### b-2. 함수 디스크립터

먼저 함수 디스크립터에 대해 알아보기 전에 메서드 시그니처에 대해 알아보자.

시그니처란 메소드의 이름과 매개변수 이름을 제외한 나머지이다.

즉 int sum(int a, double b) , int sum2(int c, double d) 이 두 메소드는 서로 시그니처가 같다.

함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킨다.

람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 부른다.

(함수형 인터페이스의 추상 메서드 시그니처를 함수 디스크립터라고 부른다.)

```java
public void process(Runnable r) {
    r.run();
}

process(()-> System.out.println("This is awesome!!"));
//process(() -> { System.out.println("This is awesome!!");});
```

Runnable 인터페이스의 유일한 추상 메소드인 run 메소드는 인수와 반환값이 없으므로,

람다 표현식의 함수 디스크립터도 해당 시그니처에 맞춰 작성됐다.

_#이때 왜 람다 표현식에 중괄호를 사용하지 않았는지 궁금할텐데, 이는 하나의 void 메소드 호출은 중괄호를 생략한다._

_@FunctioalInerface는 무엇일까?_

다음에 나오는 인터페이스가 함수형 인터페이스임을 알리는 어노테이션.

함수형 인터페이스가 아닐경우 에러 발생.

## **c. 람다 활용 : 실행 어라운드 패턴**

데이터베이스의 파일 처리를 구현한다고 상상해보자 

자원을 열고(설정), 처리한 다음에, 자원을 닫는(정리) 순으로 구현할 것이다.

이런 순환 패턴의 코드를 실행 어라운드 패턴이라 부른다.

_(다음 예시는 try with resources 구문을 사용했다. 자원을 명시적으로 닫지 않아도 된다.)_

```java
public String processFile() throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return br.readLine();
    }
}
```

이 예시를 람다로 간결하게 구현하는 과정을 한번 경험해보자!

### c-1. 1단계, 동작 파라미터화를 기억하라 

현재 코드는 파일에서 한번에 한 줄만 읽을 수 있다.

만약 요구사항이 한번에 두 줄을 읽거나 자주 사용되는 단어를 반환하라는 등으로 바뀌면 어떻게 해야하나?

그렇다! 설정과 정리 과정은 재사용하고, processFile 메소드만 다른 동작으로 작동할 수 있으면 된다!

즉 processFile의 동작을 파라미터화 하는 것이다!!!!!

자 이제 다음 단계부터 차근차근 동작 파라미터화를 구현해보자!

### c-2. 2단계, 함수형 인터페이스를 이용해서 동작 전달

람다로 활용하려면 함수형 인터페이스를 이용해야 한다.

processFile 의 시그니처와 일치하는 함수형 인터페이스를 만들어야 한다.

BufferedReaderProcessor라고 정의해 인터페이스를 구현하고, processFile 메서드의 인수로 전달해보자!

```java
@FunctionalInterface
public interface BufferReaderProcessor {
    String process(BufferedReader b) throws IOException;
}

public String processFile(BufferedReaderProcessor p) throws IOException {
//...
}
```

### c-3. 3단계, 동작 실행

자 이제 BufferedReaderProcessor에 process 메서드의 시그니처와 일치하는 람다를 전달할 수 있다.

이렇게 하면 함수형 인터페이스의 추상 메소드를 람다가 구체화해서 precessFile에서 실행할 수 있게 된다.

즉 람다로 구현된 다양한 요구조건들을 받는 그릇이 준비된 셈이다.

```java
public String processFile(BufferedReaderProcessor p) throws IOEXception {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        return p.process(br);
    }
}
```

### c-4. 4단계, 람다 전달

이제 람다를 이용해서 다양한 동작을 processFile 메서드로 전달할 수 있다.

```java
String oneLine = processFile(BufferedReader br) -> br.readLine());

String twoLine = processFile(BufferedReader br) -> br.readLine()+br.readLine();
```

## **d. 함수형 인터페이스 사용**

다양한 람다 표현식을 사용하려면 공통의 함수 디스크립터를 기술하는 함수형 인터페이스 집합이 필요하다!

자바 8에는 java.util.function 패키지로 여러가지 함수형 인터페이스를 제공한다.

### d-1. Predicate

java.util.function.Predicate<T> 인터페이스는 test라는 추상 메소드를 정의한다!

test는 T 객체를 인수로 받아 불리언을 반환한다!

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
}
public <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> results = new ArrayList<>();
    for(T t: list) {
        if(p.test(t)) {
            results.add(t);
        }
    }
    return results;
}
Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```

### d-2. Consumer

java.util.function.Consumer<T> 인터페이스는 accept라는 추상 메소드를 정의한다!

accept는 T 객체를 인수로 받아 어떤 동작을 수행하고 반환값은 없다.

for each를 Consumer로 구현한 사례를 보자

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}

public <T> void forEach(List<T> list, Consumer<T> c) {
    for(T t: list) {
        c.accept(t);
    }
}
forEach(Arrays.asList(1,2,3,4,5), (Integer i) -> System.out.println(i));
```

### d-3. Function

java.util.function.Function<T, R> 인터페이스는 추상 메서드 apply를 정의한다

apply는 T를 인수로 받아 R 객체로 반환한다.

입력을 출력으로 매핑하는 람다를 정의할 때 사용할 수 있다.

String 리스트를 인수로 받아, 각 String 길이를 포함하는 Integer 리스트로 변환하는 map메서드 구현 사례를 보자!

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}

public <T, R> List<R> map(List<T> list, Function<T, R> f) {
    List<R> result = new ArrayList<>();
    for(T t: list) {
        result.add(f.apply(t));
    }
    return result;
}

List<Integer> l = map(Arrays.asList("lambdas", "in", "action"), (String s) -> s.length());
//[7,2,6]
```

### d-4. 기본형 특화

이 글을 보고 있는 사람들은 아시다시피, 제네릭에는 래퍼클래스(책에서는 참조형이라고 한다) 인스턴스만 사용가능하다. 이런 불편을 박싱과 언박싱으로 해소할 수 있지만, 그럴 경우 불필요한 메모리 소모가 생긴다.

자바 8에서는 기본형으로 입출력할 때 오토박싱을 피하기 위해 특별한 함수형 인터페이스를 제공한다.

아래는 IntPredicate를 활용한 예시이다.

```java
public interface IntPredicate {
    boolean test(int i);
}

IntPredicate evenNumbers = (int i) -> i%2==0;
evenNumbers.test(1000); //참이고 박싱이 진행되지 않음

Predicate<Integer> evenNumbers2 = (int i) -> i%2==0;
evenNumbers2.test(1000); //참이고 박싱이 진행됨
```

Predicate와 IntPredicate의 차이를 알아차리기 바란다!

### d-5. 람다와 함수형 인터페이스 예제를 표로 정리

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr><td style="width: 33.3333%;">사용 사례</td><td style="width: 33.3333%;">람다 예제</td><td style="width: 33.3333%;">대응하는 함수형 인터페이스</td></tr><tr><td style="width: 33.3333%;">불리언 표현</td><td style="width: 33.3333%;">(List&lt;String&gt; list) -&gt; list.isEmpty()<br>리스트가 비었는지 확인</td><td style="width: 33.3333%;">Predicate&lt;List(String)&gt;</td></tr><tr><td style="width: 33.3333%;">객체 생성</td><td style="width: 33.3333%;">() -&gt; new Apple(10)</td><td style="width: 33.3333%;">Supplier&lt;Apple&gt;</td></tr><tr><td style="width: 33.3333%;">객체 소비</td><td style="width: 33.3333%;">(Apple a) -&gt; System.out.println(a.getWeight())</td><td style="width: 33.3333%;">Consumer&lt;Apple&gt;</td></tr><tr><td style="width: 33.3333%;">객체에서 선택/추출</td><td style="width: 33.3333%;">(String s) -&gt; s.length()</td><td style="width: 33.3333%;">Function&lt;String, Integer&gt;<br>ToIntFunction&lt;String&gt;</td></tr><tr><td style="width: 33.3333%;">두 값 조합</td><td style="width: 33.3333%;">(int a, int b) -&gt; a*b</td><td style="width: 33.3333%;">IntBinaryOperator</td></tr><tr><td style="width: 33.3333%;">두 객체 비교</td><td style="width: 33.3333%;">(Apple a1, Apple a2) -&gt; a1.getWeight().compareTo(a2.getWeight())</td><td style="width: 33.3333%;">Comparator&lt;Apple&gt; 또는<br>BiFunction&lt;Apple, Apple, Integer&gt;,<br>ToIntBiFunction&lt;Apple, Apple&gt;</td></tr></tbody></table>

_복잡하긴 하다._

### d-6. 예외, 람다, 함수형 인터페이스의 관계

함수형 인터페이스는 확인된 예외를 던지는 동작을 허용하지 않는다.

즉 예외를 던지는 람다 표현식을 만들려면, 함수형 인터페이스에서 예외를 선언하도록 직접 정의하거나, 람다를 try catch문으로 감싸야 한다.

예외를 처리하는 함수형 인터페이스를 선언하는 예시를 보자.

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
}
BufferedReaderProcessor p = (BufferedReader br) -> br.readLine();
```

하지만 이미 자바 API에 정의된 함수형 인터페이스를 사용하려 하는데  
예외 때문에 다시 직접 만들어 쓰기는 쉽지 않다...

이럴 때는 람다를 try catch로 감싸는 방법을 사용해보자!!

```java
Function<BufferedReader, String> f = (BufferedReader b) -> {
    try {
        return b.readLine();
    } catch(IOException e) {
        throw new RuntimeException(e);
    }
};
```

## **e. 형식 검사, 형식 추론, 제약**

람다로 함수형 인터페이스의 인스턴스를 만들 수 있는데,

정작 람다 표현식 자체에는 어떤 함수형 인터페이스를 구현하는지의 정보가 없다..

어떻게 알고 인스턴스를 만드는걸까?

### e-1. 형식 검사

람다가 사용되는 콘텍스트(람다가 전달될 메서드 파라미터, 람다가 할당되는 변수 등..)을 통해  
람다의 형식을 추론할 수 있다!

예시로 이해해보자.

```java
public <T> void filter(inventory, Predicate<T> p){
    if(p.test()){
        inventory.add(p);
    }
}

List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```

위 코드의 형식 확인 과정은 다음과 같다.

1.  람다가 사용된 콘텍스트는 뭐냐?  
    filter 메서드에서 사용됐으니, filter메서드의 정의를 확인하자!
2.  대상 형식은 뭐냐? _#대상 형식이란, 컨텍스트에서 기대되는 람다의 형식._  
    filter 메서드를 보니, 람다에 해당하는 대상형식은 Predicate<Apple>이다!
3.  Predicate<Apple> 인터페이스의 추상 메서드는 뭐냐?  
    boolean test(Apple apple)이다! _#d-1참고._
4.  아하! 그러면 해당 람다는 Apple을 받아 boolean을 반환하면 되겠군!

### e-2. 같은 람다, 다른 함수형 인터페이스

앞서 다룬 대상 형식(타겟타입)이란 개념 때문에 같은 람다 표현식도 여러 함수형 인터페이스에 사용할 수 있다!

물론 이렇게 활용되기 위해서는 해당 추상 메서드에 호환이 되어야 한다.(시그니처가 호환되어야 할 것이다.)

```java
Callable<Integer> c = () -> 43;
PrivilegedAction<Integer< p = () -> 43;
```

 위 두 코드에는 같은 람다식이 서로 다른 함수형 인터페이스에 할당 되었다.

Callable과 PrivilegedAction 모두, 인수를 받지 않고 제네릭 형식 T를 반환하는 추상 메소드가 있어서 가능하다.

_#특별한 void 호환 규칙_

람다의 바디에 일반 표현식이 있으면 void를 반환하는 함수 디스크립터(시그니처)와 호환된다.

List의 add는 T를 받아 boolean을 반환하지만 T를 받는 void 형식에도 사용 가능하다.

```java
// Consumer는 void 반환값을 가지면, 일반 표현식이 와도 괜찮다!
Consumer<String> b = s -> list.add(s);
```

### e-3. 형식 추론

람다 표현식의 파라미터 형식을 생략해도 컴파일러가 추론할 수 있다.

```java
List<Apple> greenApple = filter(inventory, apple -> GREEN.equals(apple.getColor()));
```

apple이 어떤 형식인지 말 안해줘도, filter 메소드을 통해 추론 할 수 있게된다.

형식을 써주는게 좋을 때도 있고, 생략해야 좋을 때도 있으니, 개발자가 가독성이 좋은 방향으로 선택하길 바란다!

### e-4. 지역 변수 사용(람다 캡처링)

람다 표현식에 자유 변수(파라미터로 넘겨진 변수가 아닌 외부에 정의된 변수)를 활용할 수도 있다!

```java
int portNumber =1337;
Runnable r = () -> System.out.println(portNumber);
```

람다는 인스턴스 변수, 정적 변수 모두 사용 가능하지만 제약이 있다.

명시적으로 final 선언되어 있거나, 실질적으로 final 선언된 변수여야만 한다.

즉 람다는 한번만 할당할 수 있는 지역 변수를 사용할 수 있다.

다음 예시는 컴파일 에러를 일으킨다.

```java
int portNum = 1337;
Runnable r = () -> System.out.println(portNum);
portNum=31338; //error
```

_그렇다면 왜 제약이_ _있는 건가?_

인스턴스 변수와 지역 변수의 태생적 차이 때문!

인스턴스 변수는 힙에 저장, 지역 변수는 스택에 저장된다.

람다가 지역 변수에 접근할 수 있는 상황에서 람다가 스레드에서 실행되면,  
변수를 할당한 스레드가 사라져 변수 할당이 해제되지만, 람다에서 해당 변수를 사용해야 된다.

즉 자바에서는 원래 변수에 접근하는 개념이 아니라, 원래 변수를 복사본을 제공하는 개념으로 구현한다.

따라서 복사본의 값이 바뀌면 않아야 되므로, 할당을 한번만 허용하는 것이다.

## **f. 메서드 참조**

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다.

때로는 람다보다 메서드 참조가 더 가독성이 좋을 수 있다.

```java
//람다 표현식
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));

//메서드 참조
inventory.sort(comparing(Apple::getWeight));
```

### f-1. 메서드 참조 요약

메서드 참조는 람다 표현식을 축약한 것이다.

위 코드에서 (Apple a1, Apple a2) -> .... 을 Apple::getWeight로 줄인 것이다.

그렇다면 이 코드는 어떤 효과를 가질까?

람다로 이미 존재하는 메서드를 호출하려고 하면,  
굳이 그 내용을 설명할 것이 아니라, 해당 메소드명을 직접 참조하는 것이 편리하고 가독성이 좋다.

(위 코드로 예시를 들면 getWeight메소드이 구현되어 있으면, 굳이 람다로 그 내용을 쓰지말고 메소드 명만 쓰는 메소드 참조를 하자는 것이다.)

이때 실제 메소드를 호출하는 것은 아니므로 괄호가 없다.

람다를 축약한 것임을 기억하자.

단축 표현 예시를 보자

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr><td style="width: 50%;">람다</td><td style="width: 50%;">메서드 참조 단축 표현</td></tr><tr><td style="width: 50%;">(Apple apple) -&gt; apple.getWeight()</td><td style="width: 50%;">Apple::getWeight</td></tr><tr><td style="width: 50%;">() -&gt; Thread.currentThread().dumpStack()</td><td style="width: 50%;">Thread.currentThread()::dumpStack</td></tr><tr><td style="width: 50%;">(str, i) -&gt; str.substring(i)</td><td style="width: 50%;">String::substring</td></tr><tr><td style="width: 50%;">(String s) -&gt; System.out.println(s)<br>(String s) -&gt; this.isValidName(s)</td><td style="width: 50%;">System.out::println<br>this::isValidName</td></tr></tbody></table>

_당장은 이해가 안되도 차근차근 공부해보자._

### f-2. 메서드 참조를 만드는 방법

일단 메소드 참조에는 세가지 유형이 있다

1.  정적 메서드 참조  
    Integer의 parseInt는 Integer::parseInt로 표현 가능하다
2.  다양한 형식의 인스턴스 메소드 참조  
    String의 toUpperCase는 String::toUpperCase로 표현 가능하다  
    즉 (String s) -> s.toUpperCase()를 String::toUpperCase로 축약 가능하다.
3.  기존 객체의 인스턴스 메서드 참조  
    Transaction이라는 객체를 할당받은 expensiveTransaction 지역 변수가 있고, Transaction 객체에는 getValue메서드가 있다고 할 때, expensiveTransaction::getValue라고 표현 가능하다.  
    즉 () -> expensiveTransaction.getValue()를 expensiveTransaction::getValue라고 축약 가능하다.

람다 표현식을 메서드 참조로 표현하는 단축 규칙을 보여주는 도표이다.

[##_Image|kage@dUB92e/btq9EO4XvW0/KuP3ujmb0mkTZiKm9VA580/img.jpg|alignCenter|data-origin-width="825" data-origin-height="659" data-ke-mobilestyle="widthOrigin"|||_##]

### f-3. 생성자 참조

ClassName::new처럼 클래스명과 new 키워드를 이용해서 기존 생성자의 참조를 만들 수 있다.

이는 정적 메서드의 참조를 만드는 방법과 비슷하다.

다음 예시들은 다양한 시그니처에 따른 생성자 참조를 보여준다.

```java
//람다 스타일, new Apple() 이라는 생성자가 있을 경우
Supplier<Apple> c1 = () -> new Apple();
Apple a1 = c1.get();
//생성자 참조
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();

//람다 스타일, new Apple(Integer)
Funtion<Integer, Apple> c2 = (weight) -> new Apple(weight);
Apple a2 = c2.apply(110)
//생성자 참조
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110)

//람다 스타일, new Apple(String color, Integer)
BiFUnction<String, Integer, Apple> c3 = (color, weight) -> new Apple(color, weight);
Apple a3 = c3.apply(GREEN, 110);
//생성자 참조
BiFunction<Color, Integer, Apple> c3 = Apple::new
```

이렇게 인스턴스화하지 않고도 생성자에 접근할 수 있는 기능을 다양한 상황에서 응용해보자.

```java
//사과의 무게 정보와 함께 Apple 생성자 호출
List<Integer> weights = Arrays.asList(7, 3, 4, 10);
List<Apple> apples = map(weights, Apple::new);
public List<Apple> map(List<Integer> list, Function<Integer, Apple> f) {
  List<Apple> result = new ArrayList<>();
  for( Integer i : list) {
    result.add(f.apply(i));
  }
  return result;
}
```

지금까지는 이미 존재하는 함수형 인터페이스를 활용해 생성자 참조를 했다.

만약 인수가 세개 이상을 필요로 하는 생성자의 생성자 참조는 어떻게 해야 할지 알아보자!

```java
public interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}

TriFunction<Integer, Integer, Integer, Color> colorFactory = Color::new
```

## **g. 람다, 메서드 참조 활용하기**

자 처음에 다룬 사과리스트를 다양한 요구조건에 맞춰 정렬하는 문제를 지금까지 배운 람다와 메서드 참조로 해결해보자!

우리가 얻을 최종 결과물은 다음과 같다.

```java
inventory.sort(comparing(Apple::getWeight));
```

### g-1. 1단계, 코드 전달

우리는 자바 8에 도입된 List API에서 sort 메소드를 활용해 정렬을 해결하기로 했다.

그런데 sort 메소드에 정렬 전략을 어떻게 도입할 수 있을까?

일단 sort 메소드의 시그니처를 알아보자

```java
void sort(Comparator<? super E> c)
```

위 코드는 Comparator 객체를 인수로 받아 두 사과를 비교한다.

즉 Comparator 객체에 다양한 동작을 포함시켜서 전략을 도입시킬 수 있을 것이다.

이제 'sort의 동작은 파라미터화 되었다.' 말할 수 있다.

즉 sort에 전달된 정렬 전략에 따라 sort의 동작이 달라질 것이다.

그렇다면 우리는 맨 처음 코드를 다음과 같이 바꿀 수 있다.

```java
public class AppleComparator implements Comparator<Apple> {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
}
inventory.sort(new AppleComparator());
```

자 이제 AppleComparator 클래스의 동작을 바꿔주면 요구조건에 유연하게 대응할 수 있다!

### g-2. 2단계, 익명 클래스 사용

하지만 한번만 사용할 Comparator를 1단계에서 처럼 구현하는 거 보다는 익명 클래스를 이용하는게 나아 보인다.

```java
inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
});
```

### g-3. 3단계, 람다 표현식 사용

하지만 여전히 코드가 장황하다!

자바 8에서는 함수형 인터페이스(오직 하나의 추상 메서드를 정의하는 인터페이스)를 기대하는 곳 어디에서나 람다표현식을 사용할 수 있다!

Comparator의 함수 디스크립터(추상 메서드의 시그니처)는 (T, T) -> int다.

우리의 경우는 사과를 사용하므로, (Apple, Apple) -> int로 표현할 수 있다.

```java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));

inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
```

이렇게 코드를 작성할 수 있는데, 첫 줄은 자바 컴파일러가 inventory의 제네릭 타입에 따라 람다의 파라미터 형식을 추론한다!

이 코드의 가독성을 더 높여보자!!

Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인자로 받는 정적 매서드 comparing을 포함한다!!!

이 comparing 메소드를 활용해서 더 간단하게 작성할 수 있다.

```java
import static java.util.Comparator.comparing;
inventory.sort(comparing(apple -> apple.getWeight()));
```

_람다표현식은 사과를 비교하는 데 사용할 키를 어떻게 추출할 것인지 지정하는 한 개의 인수만 포함한다._

### g-4. 4단계, 메서드 참조 사용

이제 람다 표현식을 메서드 참조를 활용해 더 간단히 만들어보자

(앞서 본 거 처럼, java.util.Comparator.comparing은 정적으로 임포트했다고 가정하자)

```java
inventory.sort(comparing(Apple::getWeight));
```

## **h. 람다 표현식을 조합할 수 있는 유용한 메서드**

람다 8에서는 몇몇 함수형 인터페이스는 다양한 유틸리티 메서드를 포함한다.

간단한 여러 개의 람다 표현식을 조합해서 복잡한 람다 표현식을 만들 수 있다는 의미다!!!

근데 함수형 인터페이스가 추가로 메서드를 제공하는 것에 의문이 생길 수 있다...

이 때 등장하는 개념이 바로 디폴트 메소드다.(추후에 관련 게시물로 추가 등록)

### h-1. Comparator 조합

이전에 g-3에서 알아봤듯이, 정적메서드 Comparator.comparing을 이용해서 비교에 사용될 키를 추출하는 Function 기반의 추출하는 Function 기반의 Comparator를 반환할 수 있다.

```java
Comparator<Apple> c = Comparator.comparing(Apple::getWeight);
```

### h-1-1. 역정렬

내림차 정렬은 다른 Comparator 인스턴스를 만들 필요가 없다!

reverse 디폴트 메소드에 제공되어 있으니 써먹으면 된다.

(reverse 메소드는 비교자의 순서를 뒤바꾸는 역할을 한다.)

```java
inventory.sort(comparing(Apple::getWeight).reversed());
```

### h-1-2. Comperator 연결

만약 무게가 같은 두 사과가 존재하면, 어떻게 정렬해야 하는가?

이런 상황에 대비해, 비교 결과를 더 다듬을 수 있는 두번째 Comparator를 만들어 연결할 수 있다.

이때 사용되는 thenComparing 메소드를 활용해 두번째 비교자를 받아 사용할 수 있다.

다음 예시는 무게로 내림차순 정렬을 하고, 만약 같은 무게를 가진 경우, 생산 국가로 다시 정렬하는 예시이다.

```java
inventory.sort(comparing(Apple::getWeight).reversed().thenComparing(Apple::getCountry));
```

### h-2. Predicate 조합

Predicate 인터페이스의 경우, 보다 복잡한 프레디케이트를 만들 수 있도록,

negate, and, or 세가지 메서드를 제공한다. (당연히 얘네는 디폴트 메서드일 것이다.)

### h-2-1. negate

negate는 부정한다는 의미다.

negate를 붙이면 앞서 진행됐던 프레디케이트를 반전시킬 수 있다.

쉽게 말해 여집합을 생각하면 편할 것이다.

```java
Predicate<Apple> notRedApple = redApple.negate();
```

빨간 사과를 구분하는 Predicate 인터페이스 redApple 뒤에 negate메소드를 더해주면, 빨갛지 않은 사과만 뽑아낼 수 있다!

### h-2-2. and

and는 두 람다를 조합할 수 있다.

```java
Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150);
```

빨간 사과를 구분하는 Predicate 인터페이스 redApple 뒤에 람다를 포함한 and메소드를 연결하면, 빨갛고 무거운 사과를 뽑아낼 수 있다.

### h-2-3. or

or는 다양한 조건을 만들어 낼 수 있다. 예시로 보면 이해가 빠를 것이다.

```java
Predicate<Apple> redAndHeavyAppleOrGreen =
    redApple.and(apple -> apple.getWeight() > 150).or(apple -> GREEN.equals(a.getColor()));
```

### h-3. Function 조합

Function 인터페이스는 andThen과 compose 두가지 메소드를 제공한다!

### h-3-1. andThen

andThen 메소드는 두 개의 Function 인터페이스를 받아, 하나의 인터페이스의 결과물을 다른 인터페이스의 입력값으로 전달하는 역할을 한다.

```java
Function<Integer, Integer> f = x -> x+1;
Function<Integer, Integer> g = x -> x*2;
Function<Integer, Integer> h = f.andThen(g);
int result = h.apply(1); //return 4
```

### h-3-2. compose

compose 메소드는 인수로 주어진 함수를 먼저 하고, 그 결과를 외부 함수의 인수로 제공한다.

이 말이 어렵게 느껴지겠지만 예시로 살펴보자.

```java
Function<Integer, Integer> f = x -> x+1;
Function<Integer, Integer> g = x -> x*2;
Function<Integer, Integer> h = f.compose(g);
int result = h.apply(1); //return 3
```

andThen의 예시에서는 4가 반환됐는데, compose는 3이 반환 됐다. 이 차이를 살펴보자.

andThen은 말그대로 f를 하고 g를 해라 라는 의미다. 매우 직관적이다.

compose는 g를 하고 f를 하라는 거다. 아까 설명을 함수 스타일로 표현하면 f(g(1))이 되어 (1\*2)+1이 되므로 3이 반환된 거다.