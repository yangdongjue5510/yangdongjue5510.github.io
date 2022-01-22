---
title: 13. Comparable과 Comparator의 차이!
date: 2021-12-03 10:29:20
tags:
category:
    - Java
    - Java Basic
---

# Comparable과 Comparator의 차이

## 결론

둘 다 객체의 비교 기준을 마련하는 인터페이스.

**Comparable**은 **compareTo(T t)**로 자기 자신과 다른 객체의 비교.
**Comparator**은 **compare(T t, T u)** 자기 자신이 아닌 다른 두 객체 간의 비교.

Comparable은 **Arrays.sort()나 Collections.sort()를 했을 경우 비교 기준을 제공**한다.
Comparator는 **새로운 비교기준이 필요할 때 익명 클래스로 활용** 가능.

## 실험

### 실험에 필요한 Student

```java
public class Student {
  private int age;
	private int score;

	public Student(int age, int score) {
		this.age = age;
		this.score = score;
	}

  @Override
	public String toString() {
		return "age = "+age+" score = "+score;
	}

	public int getAge() {
		return age;
	}

	public int getScore() {
		return score;
	}
}
```

일단 정렬 실험에 사용될 객체를 만들어봤다. 이제 age나 score에 따라 다르게 구현해보자.



### Comparable와 Comparator를 클래스에 적용하기

```java
public class Student implements Comparable<Student> {
  ...
  @Override
  public int compareTo(Student o) {
    return this.age - o.age;
  }
}
```

**Comparable**을 활용하려면 **compareTo**메서드를 구현해야 한다.

이 메서드는 자기 자신과 다른 객체를 비교한다. (return 값이 양수면 자기 자신 우선, 음수면 다른 객체 우선)

```java
public class Student implements Comparator<Student> {
  ...
  @Override
  public int compare(Student o1, Student o2) {
    return o1.age - o2.age;
  }
}
```

**Comparator**는 **compare**메서드를 구현해야 한다.

이 메서드는 서로 다른 객체 두개를 비교한다.

### 정렬 실험해보기

문제는 Comparable을 구현한 경우와 Comparator를 구현한 경우가 정렬을 할 때 다르다는 점이다!!

```java
//implements Comparable<Student> 한 경우
Arrays.sort(arr);						//성공!
Collections.sort(list);			//성공!

//implements Comparator<Student> 한 경우
Arrays.sort(arr);						//컴파일 에러!!
Collections.sort(list);			//컴파일 에러!!
```

실험해보니, **Comparable을 구현했을 때만 정렬이 가능**했다!!
Comparator를 구현한 경우 Comparable이 구현되지 않았다며 컴파일 에러를 일으켰다!



#### 그러면 Comparator는 왜 있는거에요?

Student 클래스가 Comparable을 구현하고, 정렬을 age 기준으로 오름차순으로 정렬됐다고 하자.

근데 프로그래밍하다가 Student를 score 기준으로 오름차 정렬해야 되는 요구사항이 생겼다면?
이미 age로 정렬하여 사용하고 있는 곳이 있으면, Comparable을 바꿀수도 없다.

그럴 때 **익명클래스를 활용한 Comparator**를 통해 해결한다.

```java
Collections.sort(list, new Comparator<Student>() {
  @Override
  public int compare(Student o1, Student o2) {
    return o1.getScore() - o2.getScore();
  }
});
```

