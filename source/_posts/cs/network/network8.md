---
title: 8. 네트워크 계층 :제어 평면(라우팅 알고리즘)
date: 2021-11-17 20:24:54
tags:
category:
    - Computer Science
    - Network
---

### **a. 라우팅 알고리즘**

라우팅이 송신자부터 수신자까지 라우터의 네트워크를 통과하는 좋은 경로를 결정하는 것.

여기서 좋은 경로란 최소 비용 경로를 의미.(비용은 금전적, 시간적, 혼잡 등을 폭넓게 의미)

 

#### **a-1. 라우팅 알고리즘 그래프**



![img](https://blog.kakaocdn.net/dn/cC6RbM/btq64gXA4Ak/WOrhkGvj5kodXmHrQEiXxK/img.png)



라우팅을 표현하는 데 쓰이는 도식.

일반적으로 G=(N, E) 나타냄

N : 노드의 집합, E : 엣지의 집합.

노드는 패킷 포워딩 결정이 이뤄지는 지점인 라우터

엣지는 노드들을 연결하는 물리 링크

 

c(x, y) 는 노드 x, y간의 비용을 의미한다.

이 값이 무한대면 x와 y가 이웃하지 않는다는 것을 의미

에지 (x, y)가 E에 속하면 노드 y는 노드 x의 이웃

 

#### **a-2. 중앙 집중형 라우팅 알고리즘(링크 상태 알고리즘)**

**네트워크 전체에 대한 완전한 정보**를 가지고 경로의 최소 비용을 계산

라우터들이 이웃에 대한 정보만 알고, 이를 네트워크 내부 라우터들에게 **브로드캐스트해서 정보 전달**

 

*#브로드캐스트 vs 유니캐스트*

유니캐스트는 한 곳에서 출발해서 한 목적지로 도착.

브로드캐스트는 한 곳에서 출발해서 여러 목적지로 도착.

 

#### **a-2-1. 다익스트라 알고리즘.**

링크 상태 알고리즘(모든 노드의 링크 cost를 알고 있음)

 

D(v) : 목적지 v까지의 최소 비용 경로의 비용

p(v) : v까지 현재 최소 비용 경로에서 v의 직전 노드(v의 이웃)

N': 노드 집합. 어떤 v까지 최소비용 경로가 알려져있으면 v는 N'에 포함

```python
Initialization : #초기화
    N'={u}	#출발지 u
    for all nodes v:
    	if v is a neighbor of u:
        	then D(v) = c(u,v)
        else : D(v) = 무한대

Loop
    find w not in N' such that D(w) is a minimum	#N'에 포함되지 않은 노드 중 최소 경로인 w
    add w to N'
    update D(v) for each neighbor v of w and not in N':
    D(v)=min(D(v), D(w)+c(w,v))
until N' = N
```

이 방식은 각 노드에 대해서 최소 비용 경로상의 직전 노드를 알게됨.

직전 노드를 반복해서 구하다보면 최소 비용 경로를 구축가능.



![img](https://blog.kakaocdn.net/dn/37d1r/btq66jZSOWl/5JSo74Xt6v7emDCyYDEZdk/img.png)![img](https://blog.kakaocdn.net/dn/bYlNr5/btq650zuLRm/G0JUXYMg25ldlajTpbkmt1/img.png)



 

이 방식에서 최악의 경우 얼마나 많은 계산이 필요한가?

 

첫번째 루프에서 출발지 제외 n개를 검사하여 하나를 골라 수행.

두번째 루프에서 n-1개를 검사해 하나를 골라 수행...

이런 식으로 **총 n(n+1)/2개를 검사**해야 함. 즉 **O(n^2)**의 복잡성을 가짐

 

하지만 이 방식은 혼잡에 따른 **경로 진동이 발생가능.**

경로에 골고루 분배되는게 아닌 한쪽에 쏠려서 진행.



![img](https://blog.kakaocdn.net/dn/bO1Cna/btq665tyk7u/KtTfqOWPUe1Tns2s8hLPoK/img.png)



*1. 초기에 D가 A로 1을 보내려 하고, B가 A로 1을 보내려 하고, C가 A로 e만큼의 트래픽을 보내려 할 때, 경로의 cost가 계산되었다.*

*2. 1의 결과를 토대로 최소 경로를 다시 찾으면, C-D-A 쪽 링크에 트래픽이 쏠리게 된다.*

*3. 2의 결과를 토대로 최소 경로를 다시 찾으면, C-B-A 쪽 링크에 트래픽이 쏠리게 된다.*

*4. 3의 결과를 토대로 최소 경로를 다시 찾으면, C-D-A 쪽 링크에 트래픽이 쏠리게 된다.....이후 무한 반복하게 되겠지?*

#### **a-3. 분산 라우팅 알고리즘**

**자신과 이웃한 링크에 대한 정보만** 가지고 시작.

반복된 계산과 이웃 노드와의 정보 교환을 통해 점차적으로 목적지까지의 최소 비용 경로 계산

 

#### **a-3-1. 벨만-포트 식**

x부터 y까지 최소 비용 경로 비용 dx(y)

x의 모든 가능한 모든 이웃 v 일때

벨만-포트 식 : **dx(y)= minv{c(x,v) + dv(y)}**이다.

이 식은 라우터들의 엔트리 설정에 도움이 된다.

(어떤 지역의 라우터로 가려면 **특정 라우터를 사용하는 것이 유리함을 알려줌**)



![img](https://blog.kakaocdn.net/dn/Yi8Pb/btq63MP28gp/Wa5KOitnyedK9XNdZ4vv9k/img.png)



 

#### **a-3-2. 거리 벡터 알고리즘(DV 알고리즘)**

Dx=[Dx(y): y in N] 노드 x에서 N에 속한 모든 다른 노드 y까지의 비용 추정값의 벡터.

DV알고리즘에 의해 노드 x는 다음 정보를 얻게 됨

*1. 모든 이웃 노드 v의 비용 c(x,y)*

*2. 노드 x의 거리 벡터 Dx*

*3. 이웃 노드들의 거리 벡터. Dv*

 

노드 x가 이웃 w의 거리 벡터를 받으면, 벨만-포트 식을 사용해 자신의 거리 벡터를 수정

비동기적으로 거리 벡터를 교환하다보면, 비용 추정값 Dx(y)가 실제 최소 비용 경로의 비용 dx(y)에 수렴!



![img](https://blog.kakaocdn.net/dn/blQMGx/btq62Wk5axL/v2WKwykr8joBreqcEt9Bnk/img.png)



```python
Initialization:
    for all destination y in N:
        Dx(y) = c(x,y) #y과 이웃이 아니면 c(x,y)는 무한대
    for each neighbor w:
    Dw(y)= ? for all destination y in N
    for each neighbor w
        send distance vector Dx = to w
Loop
    wait util I see a link cost change to some neighbor w or 
    until i receive a distance vector from some neigbor w
    
    for each y in N:
    	Dx(y)= minv{c(x,v) + Dv(y)}
if Dx(y) changed for any destination y:
	send distance vector Dx to all neighbors
    
forever
```

*링크 비용 변경 시->자신의 거리 벡터 갱신->최소 비용거리 비용에 변화시 이웃에 거리 벡터 보냄*

 

#### **a-3-3 링크 비용이 증감에 따른 전파속도의 차이가 존재한다.**



![img](https://blog.kakaocdn.net/dn/U9nOZ/btq67OkQ3Tr/dKgkHJmKBGkIzVvxoY60wk/img.png)![img](https://blog.kakaocdn.net/dn/ctKjxs/btq63M3CGXA/o32PN7suvKnUDYF6LyQ3g0/img.png)



*감소하는 경우*

\1. y가 가진 정보는  Dy(x)=4, Dy(z)=1, Dz(y)=1, Dz(x)=5이다.

\2. x-y의 링크가 1로 감소함을 y가 인지해 Dy(x)=1 로 수정해 이를 이웃에게 알림

\3. z는 변경된 벡터를 기반으로 Dz(x)를 5에서 2로 변경

\4. y는 z로부터 변경된 벡터를 받지만 y의 최소비용은 변화하지 않음.

 

*증가하는 경우*

\1. y가 가진 정보는 Dy(x)=4, Dy(z)=1, Dz(y)=1, Dz(x)=5이다.

\2. x-y의 링크가 60으로 증가함을 y가 인식했을 때, Dy(x)=Dy(z)+Dz(x)=1+5=6이 된다.
(여기서 Dz(x)가 5로 설정한건 당연히 잘못된 것이다. 하지만 y는 현 시점에 그를 알아차리지 못한다.)

\3. z는 변경된 벡터를 기반으로 Dz(x)=min{c(z,x), Dz(y)+Dy(x)}를 5에서 7으로 바꾼다.

\4. y는 z로부터 변경된 벡터를 받아 Dy(x)를 6에서 8로 바꾼다.(z가 Dz(x)를 바꿨으니, Dy(x)=Dy(z)+Dz(x)의 값이 변한 것)

다시 z는 y로부터 변경된 벡터를 받아...

zyzyzyz.....의 cost가 50=c(z,x)보다 커질 때까지 1~4를 반복한다.(44번 반복되어야 된다.)*
(이렇게 테이블이 설정되면 y에서 x로 데이터를 보낼 경우 y는 z로 보내고 z는 y로 보내는 라우팅 루프가 발생한다.!!!!)*

 

#### **a-3-4. 포이즌 리버스(3개 이상의 노드가 참여하는 loop에는 적용 불가)**

라우팅 루프를 방지하는 방법.

만약 z가 y를 통해서 목적지 x로 가는 경로를 설정 시, z는 y에게 x까지의 거리가 무한대라고 알림.

(상대방을 활용하여 특정 목적지로 가는 경우, 상대방이 나에게 특정 목적지에 대한 최소경로를 물으면 무한대라고 알려주기)

 

\1. y가 가진 정보는 Dy(x)=4, Dy(z)=1, Dz(y)=1, Dz(x)=5이다.

\2. x-y의 링크가 60으로 증가함을 y가 인식하여 Dy(x)를 구할 때, Dy(z)+Dz(x)=1+5무한대 c(y,x)인 60이 Dy(x)가 된다.

\3. z는 변경된 벡터를 기반으로 Dz(x)=min{c(z,x), Dz(y)+Dy(x)}를 50으로 바꾼다.

\4. y는 z로부터 변경된 벡터를 받아 Dy(x)를 60에서 c(y,z)+Dz(x)=1+50=51로 바꾼다.

 

#### **a-4. 링크 상태 알고리즘과 거리 벡터 라우팅 알고리즘의 비교**

|               | 링크 상태 알고리즘                                      | 거리 벡터 라우팅 알고리즘                     |
| ------------- | ------------------------------------------------------- | --------------------------------------------- |
| 메시지 복잡성 | n개의 노드, E개의 링크가 있을 때, O(nE)번 메시지를 전송 | 이웃 노드와 교환함. 여러 요인이 영향을 끼침   |
| 수렴 속도     | O(nE)개의 메시지가 필요한 O(n^2) 알고리즘               | 라우팅 루프나 무한 계수 문제가 발생할 수 있음 |
| 견고성        | 경로 계산이 분산되어 진행, 나름 견고함                  | 한 노드의 잘못된 계산이 전체로 확산 가능      |

#### **a-5. 정적 라우팅 알고리즘, 동적 라우팅 알고리즘**

정적 : 고정된 기준에 따라 경로 계산(홉 갯수 등)

동적 : 변동하는 가치에 따라 경로 계산(혼잡도)
