---
title: 우아한 테크코스 4기 프리코스 2주차 회고
date: 2021-12-05 00:40:27
tags:
    - 우아한 테크 코스
---

# 우아한 테크코스 4기 프리코스 2주차 회고

## 2주차 문제 & 내 제출 답안

https://github.com/woowacourse/java-racingcar-precourse

https://github.com/yangdongjue5510/java-racingcar-precourse

## 마주한 문제들

### 책임을 클래스들에게 분배하기

이번 문제는 메서드를 분리하는 것에 더해, 클래스를 분리해서 책임을 분배해야 한다는 미션을 받았다.

문제에는 Car 객체가 제시되어 활용하는 요구사항이 추가됐다.

일단 나눠주여 할 책임을 보면,

- 사용자에게 입력을 받는 책임
- 입력된 값을 검증하는 책임
- 자동차를 만드는 책임
- 자동차의 게임을 진행하는 책임
- 레이싱 결과를 분별하는 책임
- 결과를 출력하는 책임

이렇게 나눌 수 있었다.



나는  다음과 같이 클래스들에게 **책임을 나눠줬다**.

- 입력을 담당하는 InputManager
- 입력값 검증하는 ValidChecker
- 자동차를 만들어 게임을 진행하는 RacingPlayer
- 자동차의 운행 여부를 판단하여 운행하는 Car
- 자동차 위치에 따라 우승자를 찾는 RacingReferee

이렇게 역할을 나눠줬는데, 역할을 나눠주고 나니 **각 객체의 인스턴스 변수를 다른 객체가 활용해야 하는 경우가 있었다.**

#### 패키지 분리 후 default 접근 지시자

나는 이 문제를 해결하기 위해 **도매인과 관련된 Car, RacingPlayer, RacingReferee는 같은 패키지로 분리**해줬다.

그리고 **getter 메서드를 default로 추가**해 도매인 관련된 객체들만 해당 인스턴스 변수를 이용할 수 있게 했다.

예시로 RacingReferee는 우승자들을 분류해서 리스트로 가지고, 우승자의 이름을 출력하는 책임을 지고 있다.
그런데 우승자(Car 객체)는 이름이 private로 되어 있다. 이럴 경우 default getter로 캡슐화를 최대한 준수하고자 했다.

```java
public class Car implements Comparable<Car> {
	private final String name;
	...
	String getName() {	//default 접근지시자를 활용했다.
		return this.name;
	}
}
```

```java
public class RacingReferee {
	private List<Car> winners;
	...
	private void printWinners() {
		StringBuilder builder = new StringBuilder();
		...
		for (int i = 0; i < winners.size(); i++) {		//getName 메서드를 활용하고 있다.
			builder.append(winners.get(i).getName()).append(MESSAGE_COMMA_WITH_SPACE);
		}
		...
	}
}
```



### 우승자 알아내기

Car의 우승 여부를 알려면 각 Car의 위치를 알아야 한다.

그런데 나는 앞서 말했듯이 우승여부를 다른 객체(RacingReferee)가 한다고 했다.
이미 Car 객체의 위치값은 private인데 어떻게 RacingReferee가 이를 계산해서 처리할 것인가?

가장 편한 대답은 아까처럼 default getter를 만드는 방법이다.
하지만 검색을 좀 해봤더니 다른 방식을 알아냈다.

바로 **Comparable로 정렬하기** 이다.

Car를 Comparable로 정렬 기준을 만들자.

```java
public class Car implements Comparable<Car> {
	private final String name;
	private int position = 0;
	...
	@Override
	public int compareTo(Car otherCar) {
		return otherCar.position - this.position;
	}
	...
}

```

이렇게 해놓으면 외부에서 자동차 위치를 알지 못해도 compareTo로 자동차들의 위치를 비교한다!

```java
public class RacingReferee {
	private List<Car> winners;
	...
	private void findWinner(List<Car> participantCars) {
		Collections.sort(participantCars);	//
		Car winner = participantCars.get(WINNER_INDEX);
		this.winners = participantCars.stream()
			.filter(car -> car.compareTo(winner) == COMPARE_WINNER_RESULT)
			.collect(Collectors.toList());
	}
	...
}
```

RacingReferee는 Car가 어떤 식으로 정렬하는지 알지 못해도 일단 ```Collections.sort()``` 하면 우승자 순으로 정렬된다는거만 안다.

나름 캡슐화를 지킨 방식이라서 재밌었다.



## 후기

이번 주차는 비교적 수월하게 했다. 지난주에 설계를 못한다는 생각에 충격을 먹어서, 급하게 객체지향 관련 책을 찾아 본 게 큰 도움이 된거 같다.

지난 야구 게임을 구현할 때는 한 클래스가 거의 모든 데이터를 독점하고, 여러 일을 책임지고 있는 좋지 않은 설계로 구현했었다.

이번 문제는 강제로 Car 객체를 사용해야되서 자연스럽게 여러 객체들을 협력에 동참시켜야 했다.
특히 이번엔 한 클래스가 데이터를 독점하지 못하도록 어떤 부분에 다른 클래스로 대체할 것인지 고민을 많이 했다.
그 고민을 통해 등장시킨게 RacingReferee였다. 이 객체는 우승자를 가지고, 스스로 관리하게 설계했다는 점에서 만족스러웠다.

그리고 Comparator와 Comparable에 대해 공부할 수 있는 기회였다.
최대한 캡슐화를 지키면서 우승 자동차를 구하는 방법을 찾다보니 평소에 잘 이해하지 못했던 개념을 제대로 이해할 수 있었다.
이런 아이디어를 내가 직접 생각해낸게 아니라서, 만약 실제 코딩테스트 상황에서 이런 문제를 만나면 어찌해야 될 지 고민이 되긴한다.

하지만 아직 부족한 부분이 많다.
대표적으로 README 작성. 살아있는 문서를 작성하라는데, 이게 좀 힘들다.
의외라고 생각할 수 있는데, 나는 문제를 풀 때, 구현에 집중하다 보면, README는 안중에도 없게 된다.(...)
그래서 이번엔 비교적 좀 더 신경을 썼는데, 아직 부족한 거 같기도 하다...

그리고 테스트 코드 작성도 많이 부족하다.
나는 사실 이 문제를 여러번 풀어보고 제출한다.
이번 주 문제인 레이싱 문제도 대충 3번정도 풀어보고 제출하는 거다.
그래서 제출 코드에는 굳이 테스트 코드가 필요없다. 이미 많이 시도해본 코드들이기 때문이다.

근데 실전에서는 그럴 시간이 없다. 5시간안에 여러번 푼다는건 말도 안된다 ㅋㅋ.
그래서 테스트 코드를 작성하는 법을 배워야 한다는 걸 느꼈다.
JUnit와 TDD에 대해 공부하려고 한다.

아직 많이 부족하다.
