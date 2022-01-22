---
title: 우아한 테크코스 4기 최종 코딩테스트 후기
date: 2021-12-19 21:39:13
tags:
    - 우아한 테크 코스
---

# 최종 코딩테스트 문제 링크

https://github.com/woowacourse/java-pairmatching-precourse



# 아쉬운 점

## 검증 로직 중복

과정, 레벨, 미션을 입력받을 때, 해당 과정, 레벨, 미션이 존재하는지 검증해야 된다.
나는 이를 검사하는 메서드를 각 열거형 객체가 가지고 있도록 하고 findByName메서드로 이름에 맞는 객체를 가져오도록 했다.

과정을 예를 들면 다음과 같다.

```java
public enum Course {
	BACKEND("백엔드"),
	FRONTEND("프론트엔드");

	private String name;

	Course(String name) {
		this.name = name;
	}

	public static Course findByName(String input) {
		exceptionNotContain(input);
		return Arrays.stream(Course.values()).filter(course -> course.name.equals(input)).findFirst().get();
	}

	private static void exceptionNotContain(String input) {
		if (!Arrays.stream(Course.values()).anyMatch(course -> course.name.equals(input))) {
			throw new IllegalArgumentException(NO_EXIST_COURSE);
		}
	}
}
```

여기서 exceptionNotContain(String input)이 입력값이 존재하는 지 검증하고 예외를 출력한다.
만약 외부에서 과정 객체를 가져오고 싶다면, findByName(String input)을 호출하면 된다.
없는 경우는 예외를 일으키고, 있는 경우 해당 객체를 반환할 것이다.

```java
public class MatchingInformation {
	private Course course;
	private Level level;
	private Mission mission;

	private MatchingInformation(Course course, Level level, Mission mission) {
		this.course = course;
		this.level = level;
		this.mission = mission;
	}

	public static MatchingInformation of(String line) {
		List<String> information = split(line);
		Course course = Course.findByName(information.get(COURSE_INDEX));
		Level level = Level.findByName(information.get(LEVEL_INDEX));
		Mission mission = Mission.findByName(information.get(MISSION_INDEX));
		return new MatchingInformation(course, level, mission);
	}
  ...
}  
```

매칭 정보 일급컬렉션을 생성할 때, 입력받은 이름을 토대로 findByName메서드를 호출하는 것을 볼 수 있다.

문제는 과정, 레벨, 미션 이 세가지 열거형 모두 이 메서드를 가지고 있는데, 모두 중복되는 코드로 작성했다.
인터페이스를 도입하자니 검증 메서드가 static이라서 힘들었다.

## 과정, 레벨 출력

과정, 레벨, 미션을 도입하기 전에 항상 과정, 레벨, 미션을 출력해야 한다.

나는 마음이 급해서 그냥 바로 문자열로 만들어서 매번 출력하도록 처리했다.
근데 다른 PR을 보니, 과정, 레벨, 미션을 저장하고, 저장된 정보를 토대로 출력하는 사람들이 많았다.(대단..)

## 묻지 말고 시키기

객체가 가진 데이터를 가져오지 말고, 객체에게 책임을 전달해야 되는데, 기능 구현에 급급하다보니, getter 메서드로 객체에게 묻는 로직이 상당히 많았다.
프리코스에서 가장 인상깊게 생각한 피드백이고, 지키기 위해서 노력했는데 막상 시험에서 지키지 못하니 아쉽다.

## 구현하지 못한 기능

페어를 만들 수 없는 상황을 예외 처리해줘야 했는데, 시험을 풀 당시 페어를 만들 수 없는 상황이 뭔지 몰라서 구현을 못했다...

알고보니 크루가 2명보다 적으면 페어를 못만드는 상황이 생긴다는 것......
생각해보니 쉬운 로직인데 너무 긴장해서 구현하지 못한 거 같다. 시간이 부족하기도 했고.

# 후기

돌이켜보면 믿기지 않는 한달이었다.
설마 되겠냐는 생각으로 서류 지원했는데 덜컥 붙어서 정신없이 프리코스를 진행한 것 같다.
다른 지원자들의 PR을 보니 내가 우물 속 개구리 인걸 깨달았다.

3주 동안 정말 많은 걸 배웠다.
의식있는 노력을 통해 익숙함을 벗어나서 더 좋은 코드를 추구하는 마음가짐을 배웠다.
평소 잘 모르는 걸 알면서 방치했던 git 사용법도 이번 기회로 정리해서 배웠다.
하드코딩을 제거해서 다른 사람들이 봐도 이해할 수 있는 코드를 작성할 수 있음을 배웠다.
한 메서드가 한 역할을 수행하라는 원칙도 알고는 있었는데, 실천을 통해 더 자세히 체화했다.
예외가 발생하는 상황을 처리해서 프로그램이 다시 입력할 수 있도록 하는 방법을 배웠다.
객체지향 설계를 직접 실천해보고 객체 지향의 기본 개념들을 배웠다.

프리코스를 해보니, 우아한 테크코스에 가고 싶은 생각이 더 깊어졌다.
혹시 내가 자격이 있을까 고민하는 개발 지망생들이 있다면, 꼭 지원해서 프리코스까지 체험해보길 적극 추천한다.
