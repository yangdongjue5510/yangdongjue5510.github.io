---
title: 메시지와 인터페이스
date: 2021-12-16 20:13:44
tags:
    - 오브젝트
category:
    - Java
---

# 메시지, 인터페이스

메시지는 객체들이 협력하기 위해 사용할 수 있는 유일한 의사소통 수단이다.
메시지는 오퍼레이션 이름과 인자로 구성된다.

receiver.message(argument); 이렇게 메시지를 구성할 수 있다.

### 메시지와 메서드

그렇지만 메시지가 한 메서드를 가르키는 건 아니다.
메서드는 메시지를 수신하는 객체, 인자에 따라 달라진다.
메서드는 메시지를 수신했을 때 실행되는 함수를 의미한다.

이렇게 메시지와 메서드를 구분하면,
메시지 전송자와 수신자가 느슨하게 결합된다.
전송자는 그냥 내가 어떤 메시지를 보낼 지만 생각하면된다.
수신자는 그냥 메시지에 따라 메서드를 실행하기만 하면된다.

전송자는 수신자가 어떻게 메시지를 처리하는 지 감춰지게 된다.

### 퍼블릭 인터페이스

퍼블릭 인터페이스는 객체가 의사소통을 위해 공개하는 메시지의 집합이다.
퍼블릭 인터페이스에 포함된 메시지를 오퍼레이션이라고 부른다.
(오퍼레이션은 어떤 행동에 대한 추상화된 것이라고 보자.)

시그니처는 오퍼레이션 이름과 파라미터 목록을 합친 것이다.
시그니처에 구현을 더하면 메서드가 된다!

# 좋은 인터페이스를 위해서

인터페이스는 **최소한으로, 추상적이어야 한다.**

이런 원칙을 지키기 위해서는 책임주도 설계가 하는게 좋다.
메시지를 먼저 선택해서 협력에 상관없는 오퍼레이션을 제거할 수 있다.
메시지가 객체를 선택하도록 해서 객체의 의도를 메시지에 표현 할 수 있다.

다음 규칙을 통해 좋은 인터페이스 규칙을 알아보자.

### 디미터 법칙 : 협력 경로를 제한하라

낯선 객체와 협력하지 말고 이웃과 협력하라.
객체가 특정 조건을 만족하는 대상에게 메시지를 전송하도록 제한.

한 객체는 다음 대상들에게 메시지를 전송하도록 제한하자.

1. <u>this 객체</u>
2. <u>메서드 매개변수</u>
3. <u>this의 속성</u>
4. <u>this의 속성인 컬렉션의 요소</u>
5. <u>메서드 내에서 생성된 지역 객체</u>



이런 식의 코드는 디미터 법칙을 위반하는 예시다.

```java
public Reservation reserve(Screening screening) {
  ...
  screening.getMovie.getDiscountConditions(); //위반!!
  screening.calculateFee(audienceCount);	//준수!!
  ...
}

```

위반코드는 Movie 객체에게 메시지를 전달하고 있다.
Movie 객체는 위 다섯가지에 속하지 않는다!!
연쇄적으로 메시지를 전달하는 코드를 **기차 충돌 코드**라고 한다.

준수코드는 screening에게 메시지를 전달하므로 디미터 법칙을 준수했다.

하지만 **디미터 법칙은 객체의 응집도가 낮아질 수 있다.**

#### 묻지 말고 시켜라

메시지 전송자는 메시지 수신자의 상태를 기반으로 결정 내리고 수신자의 상태를 바꾸면 안된다!!!
전송자가 수신자의 상태를 기반으로 결정하는 건 캡슐화 위반이다!

묻지 말고 시켜라는 객체의 상태와 행동이 서로 밀첩하도록 설계하게 한다.

### 의도를 드러내는 인터페이스

인터페이스의 메서드는 어떻게가 아닌 무엇을 하는지 드러내도록 하라.
무엇을 하는지 드러낸다는 것은, 그 인터페이스의 메서드가가 어떤 책임을 지는지를 표현하는 것이다.
절대 내부 구현을 메서드의 이름으로 표현하지 않는다.

```java
public class PeriodCondition {
  public boolean isSatisfiedByPeriod(Screening screening) { ... }
}

public class SequenceCondition {
  public boolean isSatisfiedBySequence(Screening screening) { ... }
}
```

위 코드는 상영 정보를 받아서 해당 할인 조건에 맞는 지 검증한다.
그러나 위 코드의 메서드들은 클라이언트 입장에서 동일한 일을 한다. 그런데 이 두 메서드가 연관있음을 알 수 없다.
또한 판단 기준이 약간 수정된다면 메서드 명을 변경해줘야 되는 일이 생긴다.(캡슐화가 안된다는 소리.)

다음 코드로 바꿔주는게 적절하다.

```java
public interface DiscountCondition {
  boolean isSatisfiedBy(Screening screening);
}

public class PeriodCondtion implements DiscountCondition {
  public boolean isSatisfiedBy(Screening screening) {...}
}

public class SequenceCondition implements DiscountCondition {
  public boolean isSatisfiedBy(Screening screening) {...}
}
```



> 결론

객체에게 묻지 말고 시키되, 구현 방법이 아닌 클라이언트의 의도를 드러내는 메시지를 전달하자.

