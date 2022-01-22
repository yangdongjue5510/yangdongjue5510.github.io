---
title: 6. 네트워크 계층(라우터)
date: 2021-09-17 13:06:01
tags:
category:
    - Computer Science
    - Network
---
### **a. 네트워크 계층의 목적.**
세그먼트를 받아서 데이터그램으로 만들고, 각 라우터들이 헤더필드를 검사하여 알맞은 곳으로 데이터를 전달.
forwarding : 라우터의 입력부터 출력까지 필요한 기능들
routing: 소스로부터 목적지까지의 경로를 결정(routing algorithms)

### **b. Data Plane과 control plane**
data plane : 라우터가 자신이 가진 정보(routing table, forwarding table)를 활용해 경로 결정하는 기능(routing, forwarding)
control plane : 소스-목적지에서 어떤 라우터를 만나게 할 것인가를 결정.(traditional routing algorithms과 software defined networking(sdn)방식 존재)

b-1. traditional routing algorithms방식
각 라우터마다 가지고 잇는 라우팅 알고리즘이 control plane에서 상호작용하여
routing table을 각 라우터마다 만들어낸다.
데이터가 각 라우터에 올 때마다,  라우터는 라우팅테이블을 활용해 경로를 결정한다.(data plane)

b-2. SDN 방식
따로 만들어진 remote controller(control plane에 속함)가 각 라우터(data plane)에 있는 control agents(ca)를 제어. 각 라우터들은 서로 상호작용하지 않음.(중앙집중 방식)
flow of datagram : datagram의 집합.


## 라우터
### **a. 라우터**

![](/img/network/network6-1.png)

라우터를 간단하게 도식화한 그림이다.
녹색, 청색, 적색 네모가 묶인 것을 네트워크 인터페이스라고 부른다.
들어오는 통로를 input ports, 나가는 통로를 output ports라고 부른다.
라우팅 프로세서에 의해 스위칭패브릭이 아웃풋 포트를 지정해 보낸다.(포워딩)

### **b. input port fucntions**
![](/img/network/network6-2.png)

line termination : 라우터의 물리 계층. 전자기 신호를 비트로 바꿔주는 역할
data link layer : 데이터들을 모아 한 프레임으로 만듬(프레이밍), 그 외에도 많은 역할.(이더넷도 여기에 포함)
ip (빨간색 박스) : lookup, (포워딩 테이블을 보고 포워딩 해야할 곳을 찾는 행위), forwarding, queueing(보낼 정보들을 저장.) \*이때 lookup&forwarding은 목적지만 활용하는 경우(destination-based), 헤더의 다른 필드를 활용하는 경우(generalized. sdn)

b-1 matching : 현재 내가 전달해야되는 패캣의 헤더에 있는 목적지 주소와 포워딩 테이블을 매칭
Longest prefix matching : ip주소를 2진수로 표현하여, 앞부터 하나씩 비교하여, 더 길게 일치하는 것을 선정.

![](/img/network/network6-3.png)

b-2. Switching fabric
메모리 방식 : 입력값을 메모리에 저장하여 출력 하는 곳에서 읽어들임.
버스 방식 : 복도처럼 길을 뚫어놓은 상황. 동시에 여러 곳에서 전송할 수 없음. 대기시간이 길어짐.
크로스바 방식 : 스위치를 사용하여 사용할 경우에만 해당 경로에 스위치를 켜서 작동시킴.

![](/img/network/network6-4.png)

b-3. input port queuing
입력 쪽에서 큐잉을 사용하면 효과적이긴 하지만..
Head of the Line(HOL) 블로킹 문제가 생김.

![](/img/network/network6-5.png)

세번째 입력포트에서 초록색 데이터는 자기 앞의 빨간색 때문에 쓸데없이 기다리고 있다.
만약 세번째 입력포트에서 초록색과 빨간색을 서로 바꾸면 그런 딜레이는 사라질 것.

### **c. output ports**

![](/img/network/network6-6.png)

input port랑 거꾸로 작동한다고 생각하자.
output queuing은 congestion을 일으킬 수 있고 delay나 lost가 생길 수 있다.
queuing은 저장한 데이터를 순서를 정해 내보낼 수 있다.(스케줄링)

c-1 Scheduling mechanisms, priority
버퍼가 얼마나 찼을때 어떤 걸 버릴 것인가, 어떤걸 언제 보낼 것인가를 정함
priority scheduling :우선순위에 따라 버퍼를 여러개 운영하여 우선순위를 먼저 보냄.
![](/img/network/network6-7.png)

Round Robin scheduling : 온 순서대로 하되, 한쪽 데이터만 보내지 않고 골고루 보내는 방식
![](/img/network/network6-8.png)

Weighted Fair Queuing : Round Robin을 기반으로 하되, 데이터들을 가중치를 부여하여 차이를 두는 방식
![](/img/network/network6-9.png)