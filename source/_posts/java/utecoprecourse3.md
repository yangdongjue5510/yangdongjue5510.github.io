---
title: 우아한 테크코스 4기 프리코스 3주차 회고
date: 2021-12-10 20:56:16
tags:
    - 우아한 테크 코스
---

# 우아한 테크코스 4기 프리코스 3주차 회고

## 3주차 문제 및 내 답안 링크

https://github.com/woowacourse/java-vendingmachine-precourse

## 마주한 문제들

### MVC 패턴 적용

MVC 패턴은 Model, View, Controller를 기준으로 객체들을 분류해서 프로그램을 만드는 패턴이다.
2주차 피드백에 비즈니스 로직에 출력 관련 로직을 같이 두지 말라는 피드백이 있었다.
3주차에는 MVC 패턴을 도입해서 View 관련 로직을 분리하고자 했다.

Model은 데이터를 저장하고, 일관성 있게 처리하는 역할,
Controller는 사용자의 입력을 받고, view를 생성하거나 수정하고, model에 접근하여 데이터를 가져오는 역할,
View는 사용자에게 보여지는 부분을 구현하는 역할.

##### 컨트롤러 예시

```java
public class InputMoneyController {
	public void inputMoneyControl() {
		InputMoneyView.messageInputMoney();
		inputMachineMoney();
	}

	private void inputMachineMoney() {
		MoneyRepository.initializeMachineMoney();
	}
}
```

내 답안에 쓰인 컨트롤러 중 일부다.
이 객체는 자판기에 돈을 입금해야 하는  상황을 책임진다.
자판기는 사용자에게 돈을 입금하라는 메시지를 출력해줘야 한다.

출력은 InputMoneyView의 책임이다.
InputMoneyView.messageInputMoney()는 입금할 때 필요한 메시지를 출력하라는 의미다.

##### 뷰 예시

```java
public class InputMoneyView {
	public static void messageInputMoney() {
		System.out.println(MESSAGE_INPUT_MONEY);
	}
}
```

뷰가 메시지를 받으면 적절한 메시지를 출력하게 된다.

##### 모델 예시

위 컨트롤러를 보면, MoneyRepository.initializeMachineMoney(); 코드가 보일 것이다.
컨트롤러가 모델에게 메시지를 전달하고 있다.
기계에 돈을 초기화해서 저장해달라는 메시지를 전달한다.

```java
public class MoneyRepository {
	private static Money vendingMachineMoney;
  
	public static void initializeMachineMoney() {
		vendingMachineMoney = new Money();
	}
}
```

MoneyRepository는 내가 만든 코드에서 모델에 해당하는 객체다.
자판기에 저장되는 돈을 관리하는 책임을 진다.
initializeMachineMoney()를 통해 기계가 가진 돈을 초기화해서 저장할 수 있다.

### 일급 컬렉션 도입하기

일급 컬렉션은, 컬렉션을 감싸는 클래스를 만들어서 사용하는 방법이다.
다음과 같이 컬렉션을 그냥 쓰지 말고, 멤버 변수가 컬렉션 하나뿐인 일급 컬렉션을 도입하면 이익이 있다.

```java
public class MachineCoins {
	private Map<Coin, Integer> machineCoins = new LinkedHashMap<>();

	MachineCoins() {
		initializeChangeCoins();
		setMachineCoins(MoneyRepository.getMachineMoney());
	}
	...
}
```

위 코드는 자판기 기계에 동전을 담당하는 객체다.
이 객체가 생성되려면, initializeChangeCoins()와 setMachineCoins(MoneyRepository.getMachineMoney())를 실행한다.

map에 각 동전과 그 갯수를 0개로 초기화하고,
기계가 가진 돈만큼 동전을 만들어서 객체가 보관한다.
즉 MachineCoins는 자판기 기계가 가진 돈만큼 동전으로 만들어서 Map에 저장하는 자료구조이다.

만약 MachineCoins라는 객체가 아닌 그냥 Map<Coin, Integer> 객체로 자판기의 동전을 관리하면 문제가 있다.
위에서 얘기했듯이, 동전 갯수는 기계가 가진 돈만큼만 만들어야 한다.

일반 Map을 사용하면 해당 객체가 그런 조건을 달성했는지 알 수 없다.
혹은 다른 사람이 코드를 봤을 때, 조건이 필요한지 알 수 없다. (그냥 Map일 뿐...)

이때 MachineCoins같이 일급 컬렉션을 사용하면 조건을 달성했음을 보장할 수 있다.
그리고 조건을 검증하고, 상태와 행동을 한 클래스에서 확인 할 수 있어서 다른 사람이 파악하기도 편하다.

그리고 객체에 값을 수정하는 메소드를 만들지 않으면, 그 객체의 값을 불변으로 보장할 수 있기도 하다.

> 참고

https://jojoldu.tistory.com/412

### 맵 순회하는 방법 (Map Entry, Iterator)

동전 정보를 Map에 저장했었는데, 동전을 출력하는 로직에서 Map을 순회해야 했었다.

그냥 for문을 쓰면 될 줄 알았는데, 잘 되지 않았다.
다음은 내가 찾은 방법이다.

```java
Map<Coin, Integer> map = new LinkedHashMap<Coin, Integer>();
for (Map.Entry<Coin, Integer> entry : map.entrySet()) {
  int key = entry.getKey();
  int value = entry.getValue();
  //map.remove(key); 이건 될까?
}
```

위 코드는 순회는 잘된다. map의 원소를 수정하면 concurrentmodificationexception가 발생한다.
그럴 땐 iterator를 도입한다.

```java
Iterator<Coin> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
  iterator.next();
  iterator.remove();
}
```

이런 식으로 구현하면 순회와 동시에 삭제가 가능하다!



# 후기

긴장감 속에 치뤄진 프리코스가 끝을 향하고 있다.
사실 서류 통과가 될 지도 긴가민가했었는데, 어느새 프리코스를 마치고 최종 코딩테스트만 남기고 있다.
프리코스를 하기 전에는 내가 자바를 해본 경험이 꽤 있어서 무난하게 프리코스를 통과하지 않을까 싶었는데 오산이었다.

매 미션마다 다른 분들이 제출한 PR을 보면 엄청난 고수들이 많았다.
그럴 때마다 많은 좌절감을 느끼기도 했지만, 오히려 만약 이런 분들에게 피드백 받고 같이 공부하면 
정말 소중한 기회일 것이고, 그래서 더 프리코스 과제에 더 집중하고자 했다.

프리코스는 매 주마다 미션을 하나씩 주고, 미션을 구현할 때 지켜야할 규칙을 제시한다.
정말 놀라운 건, 이 규칙들이 몇가지 안되는데, 이 규칙들을 지키기가 굉장히 까다롭다는 사실이다.

메서드 길이 15줄을 넘어가면 안되고, 메서드의 들여쓰기가 2를 넘으면 안된다.
이 두가지만 지키려고 해봐도 굉장히 까다롭고, 코드가 완전히 새로워진다.

그리고 클래스에게 책임을 나눠주고 여러 클래스들이 협력하도록 설계하는 게 재밌었다.
사실 스프링을 쓰다보면, 요구사항이 비슷한 경우가 많아서 설계도 비슷하게 반복되는 경우가 많았다.
그래서 설계를 크게 고민하지 않았던 적이 많은데 이번 프리코스는 스스로 설계를 고민해야 되는 부분이 많아서 흥미로웠다.

코딩 실력을 향상시키기에는 3주는 짧다고 볼 수 있지만, 
프리코스를 하면서 정말 극적으로 코드 보는 시선이 넓어지고, 코드 짜는 좋은 습관도 생긴 것 같다.

프리코스의 모든 과정은 인터넷에 공개되어 있다.
이번 프리코스를 하면서 왜 진작 이런 자료를 찾아서 공부하지 않았는지 생각이 날 정도로 정말 귀한 경험이었다.

우아한 테크 코스 지원 여부를 떠나서, 프리코스는 초보 자바 개발자라면 반드시 참고하길 바란다.
