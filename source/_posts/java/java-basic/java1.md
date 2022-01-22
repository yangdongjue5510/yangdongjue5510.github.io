---
title: 함수와 메서드
date: 2021-07-30 19:41:02
tags:
category:
    - Java
    - Java Basic
---

## 함수 호출과 메모리

```java
public static int add(int num1, int num2){
    int result;
    result = num1 + num2;
    return result;
}
public static void main(String[] args){
    int n1  = 20;
    int n2  = 10;
    int total = add(n1, n2);

}
```
> 이런 식으로 코드가 짜여 있을 때, 스택 메모리에는 어떤 일이 생길까??

일단 스택 메모리에 main함수가 놓이게 된다.
그러면 main함수의 지역변수 n1,n2,total이 놓이게 되는데, 이때 add함수가 호출된다.


add함수가 호출되면, main 함수 위에 add함수가 놓이게 되어 add 함수의 지역변수가 호출된다.(num1, num2, result)


스택 메모리이므로 나중에 들어온 add함수를 먼저 해결하고, main함수로 돌아가 마저 마무리하게 된다.

![](/img/javaBasic/java1-1.png)
## 함수와 메서드의 차이?

함수는 어디에 속하지 않은 함수. 메서드는 클래스에 속한 함수로 생각하자.