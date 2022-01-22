---
title: 9. 네트워크 계층 제어 평면(OSPF, BGP)
date: 2021-11-17 20:27:30
tags:
category:
    - Computer Science
    - Network
---

### **a. OSPF** 

#### **a-1. 자율 시스템 (AS)**

확장 문제 : 수억개의 라우터가 있는데, 각 라우터의 목적지 라우팅 정볼르 저장하려면 어마어마한 메모리가 필요하다

관리 자치 문제 : ISP는 자신의 네트워크를 원하는대로 운용하거나, 네트워크 내부 구성을 외부에 감추길 원한다.

 

이를 자율 시스템 (AS)로 해결한다.

AS는 동일한 괄리 제어하에 있는 라우터의 그룹으로 구성된다.

이 AS들은 고유한 AS넘버로 구별된다.(ASN)

한 AS안에는 동일한 라우팅 알고리즘을 사용하게 되고,

이를 AS 내부 라우팅 프로토콜이라고 부른다.

 

Intra-AS : AS 내부 프로토콜

Inter-AS : AS 외부 프로토콜

gateway router : 다른 AS와 연결되는 라우터



![img](https://blog.kakaocdn.net/dn/bpxMAl/btq65d0os3v/KVX29xjOlLJkLnNd2BqtJ0/img.png)



Inter-AS는 어떤 AS에 갔을 경우 도달할 수 있는 라우터를 자신의 AS 내 라우터들에게 알려주야 한다.

 

#### **a-2. 개방형 최단 경로 우선 프로토콜 (OSPF)**

intra AS 라우팅 프로토콜의 일종.

다익스트라 알고리즘을 사용하는 링크 상태 알고리즘.

라우터는 링크 상태를 AS 내부 모든 라우터에게 flood.

각 라우터는 전체 AS에 대한 완벽한 토폴로지 지도(즉 그래프)를 얻는다.

OSPF는 링크의 가중치 설정 방법에 특정하지 않고, 주어진 링크 가중치에 대해 최소 비용 경로를 결정하는 방법을 제공

*모든 링크 가중치를 1로 하여 최소 홉 라우팅을 하거나, 링크 용량에 반비례하는 가중치를 주어 적은 대역폭의 링크 사용을 최소화하는 라우팅을 할 수 있다.*

 

보안 : 인증을 통해 신뢰할 수 있는라우터들만이 AS 내부의 OSPF 프로토콜에 참여

복수 동일 비용 경로 : 한 목적지에 동일한 비용을 가진 여러 경로가 존재할 경우, 이 경로들을 사용할 수 있음

*ip 헤더의 Tos 필드를 사용해서 각 링크마다 다양한 cost를 사용할 수 있다. (hop갯수, 대역폭...)*

멀티캐스트 라우팅과 유니캐스트 라우팅의 통합 지원

 

단일 AS 내에서 계층 지원



![img](https://blog.kakaocdn.net/dn/bsWnwm/btq63NVSF0a/2x8TMZOfZMdK1KAqXYtpok/img.png)



로컬 area, backbone으로 two-level hierarchy구조.

link-state advertisement를 해당 area 안에서 진행.

area border router가 area 외부의 라우팅을 책임짐

OSPF 영역은 backbone에 한정됨.

boundary router가 외부 AS와 연결

 

### **b. ISP간의 라우팅 : BGP(Border Gateway Protocol, 경게 게이트웨이 프로토콜)**

패킷이 여러 AS를 지나야 할 때 어떻게 처리할 것인가?

 

모든 AS들은 BGP라고 불리는 Inter AS 라우팅 프로토콜을 사용함.

BGP는 CIDR형식으로 표현된 주소의 앞쪽 접두부를 향해 포워딩한다.

 

#### **b-1. BGP의 역할**

\1. 이웃 AS로부터 도달 가능한 서브넷 접두부 정보를 얻는다.

*-나는 존재하고 여기 있습니다. by 서브넷.*

-BGP는 인터넷의 모든 라우터들이 이 서브넷에 대해 알도록 한다.

\2. 서브넷 주소 접두부로의 가장 좋은 경로를 결정

-BGP의 경로 결정 절차에 따라 좋은 경로를 결정한다.

 

#### **b-2. BGP 경로 정보 알리기**



![img](https://blog.kakaocdn.net/dn/bHjssL/btq635ClIGQ/SjF01fw978kNrd6q2calak/img.png)BGP의 추상적 연결 그래프



eBGP = 외부 BGP연결

iBGP = 내부 BGP연결

*iBGP는 물리적인 링크와 항상 일치하지 않음*

 

이때 x에 대한 도달 가능성 정보를 모든 라우터에게 알리는 작업을 가정해보자.



![img](https://blog.kakaocdn.net/dn/oE2XF/btq66jeMGqQ/AK1Ga2Lr2AIAykfYKKPEA1/img.png)



먼저 AS3이 AS2에게 BGP 메시지를 보내 x가 존재하고 AS3 안에 있음을 알린다.

그 다음 AS2는 AS1에게 "x는 존재하고 x로 가려면, AS2를 거쳐 AS3로 가야함"을 알린다.

이 과정에서 iBGP와 eBGP가 모두 사용된다.

 

#### **b-3. 최고의 경로 설정**

라우터가 BGP 연결을 통해 주소 접두부를 알릴 때 BGP 속성을 함께 보낸다.

*접두부 + BGP 속성 = 경로 (BGP에서는 경로를 이렇게 정의)*

AS-PATH : 알림 메시지가 통과하는 AS들의 리스트. 메시지의루프를감지하고 방지.

NEXT-HOP : AS-PATH를 시작하는 라우터 인테페이스의 IP 주소

이 두가지가 주로 사용.



![img](https://blog.kakaocdn.net/dn/xWduQ/btq65sDjHBW/ujWhqQ2FK0O6Mggcrl3EDK/img.png)



AS1의 라우터들이 라우터 X로 가는 경로가 있다고 할 때, NEXT-HOP은 2a로 향하는 인터페이스 IP 주소이고,

AS-PATH는 AS3,X 와 AS2,AS3,X 이렇게 두가지를 광고로 받는다.

여기서 경로 선택은 정책에 따라 달라진다.

 

#### **b-3-1. 뜨거운 감자 라우팅**

가능한 모든 경로 중, 경로의 시작점인 NEXT-HOP 경로 비용이 최소가 되는 경로가 선택된다.



![img](https://blog.kakaocdn.net/dn/claYQO/btq64xE7mNl/DAPPlfkaLHlyD1SMqOWDx1/img.png)



2d에서 x로 데이터를 보내려 할 때, 2d는 AS1,AS3,X 경로와 AS3,X 경로 두가지를 받는다.

AS2 내부 프로토콜을 통해 얻은 라우팅 정보로 가장 적은 비용의 게이트웨이인 2a를 선택한다.

2a로 가는 인터페이스 i를 결정한 이후, 포워딩 테이블에 (i,x) 경로를 추가한다.

위 예시에 따르면 2d는 AS1,AS3,X 경로로 데이터를 전송한다.

 

#### **b-3-2. 경로 선택 알고리즘**

AS-PATH와 NEXT-HOP 뿐만 아니라 지역 선호도를 BGP 속성에 추가해 경로를 선택하는 라우팅 알고리즘

이때 지역 선호도는 네트워크 관리자의 정책에 따른다.

가장 높은 지역 선호값을 가진 경로를 선택하는데, 여러 개라면 이중 최단 AS-PATH를 가진 경로로 선택한다.

이러고도 여러 경로가 존재하면, 뜨거운 감자 라우팅을 실행해 경로를 선택한다.

 

#### **b-4. 라우팅 정책**



![img](https://blog.kakaocdn.net/dn/Kyhmn/btq64ziFhGV/fh35zue71AeO7JPeuIluNK/img.png)



A,B,C = 백본 제공자 네트워크

W,X,Y = 액세스 ISP

ISP액세스 네트워크로 들어오는 모든 트래픽은 그 네트워크를 목적지로 해야만 한다

ISP액세스 네트워크에서 나가는 트래픽은 그 네트워크 안에서 생성된 것이어야만한다.

 

이때 X는 두 백본 제공자 네트워크와 연결되어 있는 다중 홈 액세스 ISP이다.

X는 과연 어떻게 B와 C간의 트래픽을 포워딩하는것을 방지할 수 있을까?

BGP 경로의 알림 방식을 제어함으로 해결된다.

X는 B와 C에게 자기 자신을 제외하고는 어떤 다른 목적지로도 경로가 없다고 알림으로써 방지할 수 있다.

 

A, B, C는 당연히 자신이 해도 되지 않을 트래픽(B-C를 지나는 트래픽에 A는 자신의 망을 내놓지 않을 것이다.)을 부담하지 않을 것이다.

이를 위한 규칙은 ISP 백본 네트워크를 통해 흐르는 트래픽은 해당 고객 네트워크를 출발지로 하거나 목적지로 해야한다는 것이다. 이는 iBGP 정책을 활용해 구현될 수 있다.
