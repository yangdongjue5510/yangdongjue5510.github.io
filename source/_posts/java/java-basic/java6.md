---
title: 6. JVM 구조와 가비지 컬렉터
date: 2021-08-30 13:18:40
tags:
category:
    - Java
    - Java Basic
---
## JVM 구조
![](/img/javaBasic/java6-1.png)
아시다시피, 자바는 운영체제의 종류와 무관하게 실행가능하다.
자바 코드를 컴파일해서 얻은 바이트 코드를 해당 운영체제가 읽을 수 있는 기계어로 바꿔준다.
JVM은 크게 4가지로 나뉜다.


**1. Class Loader**
자바로 코드를 작성하면 java파일이 생성된다.
.java파일을 컴파일하면 .class파일(바이트 코드)이 생성된다.
이렇게 생성된 .class파일들을 JVM이 할당받은 메모리 영역인 Runtime Data Area에 적재하는 역할을 Class Loader가 한다.

> 여러 종류의 클래스 로더

- Bootstrap ClassLoader : jre의 lib폴더의 rt.jar 파일 속 기본 자바 API 라이브러리 로드.
- Extension ClassLoader : jre의 lib폴더의 ext폴더에 모든 확장 코어 클래스파일 로드.
- Application ClassLoader : 사용자가 만든 클래스파일 로드.


**2. Execution Engine**
Class Loader가 적재한 클래스(.class 바이트 코드)를 기계어로 번경해 명령어 단위로 실행한다.


**3. Garbage Collector**
Heap, Stack, Method 메모리 영역에 생성된 객체들 중 참조되지 않은 객체를 찾아 제거한다.


**4. Runtime Data Area**
JVM의 메모리 영역. 어플리케이션을 실행할 때 쓰이는 데이터를 적재.
이 영역도 여러갈래로 나뉜다.


### 자바 런타임 메모리 구조
![](/img/javaBasic/java6-2.png)
**1. Method Area**
모든 클래스의 정보가 저장되는 공간.
클래스의 필드와 메소드 정보, Constant Pool(상수 풀: 리터럴 상수 값, 객체 참조를 저장), static 변수 등을 생성하고 저장


**2. Heap Area**
모든 인스턴스 오브젝트가 저장되는 공간.
new 키워드로 생성된 객체가 저장되는 영역.
메소드 영역에 있는 클래스만 생성이 가능. GC가 참조되지 않는 메모리를 확인하고 제거


**3. Stack Area**
지역 변수, 파라미터, 리턴 값, 임시 값 등 저장되는 곳
각 쓰레드마다 개별적으로 스택이 생성된다.
메소드를 호출할 때 스택에 스택 프레임이라는 스택 메모리가 쌓이는 방식.


**4. PC Register(CPU의 레지스터와 다름)**
쓰레드가 생성될 때마다 생성되는 영역. 
Program Counter, 즉 현재 쓰레드가 실행되는 부분의 주소와 명령을 저장.


**5. Native Method Stack**
자바 외 언어로 작성된 코드를 위한 메모리 영역.


여기서 heap, Method Area는 쓰레드끼리 공유하고,
stack, pc register, native method stack은 서로 공유되지 않고 각자 생성한다.


### heap과 GC
![](/img/javaBasic/java6-3.png)
원래는 JVM이 관리하지만, 최신 자바는 OS과 관리해서
할당받아 실행하지 않는다는 장점이 있다.


Heap 영역은 5개 영역으로 나뉜다.
**eden, survivor1, survivor2, old, permanent**


**Minor GC : New 영역에서 일어나는 GC**
최초에 객체가 생성되면 eden 영역에 생성된다.
eden이 가득차면 survivor1 영역에 메모리 그대로 복사 후 survivor1 제외한 영역 객체 제거
이후 eden, survivor1 모두 차면, 그 중에 참조되고 있는 객체가 있는지 확인
참조되고 있는 객체만 survivor2로 이동 후 다른 영역 객체 제거
위 과정을 여러번해서 survivor2에 남으면, old영역으로 이동시킨다.


**Major GC : old 영역에서 일어나는 GC**
old 영역에서 참조되지 않은 객체들을 모아 한번에 제거한다.
제거되어 heap 중간중간 빈 공간을 다시 재구성해 채운다.
(그래서 GC가 일어나면 GC를 다루는 쓰레드 이외엔 모든 쓰레드가 정지한다.)