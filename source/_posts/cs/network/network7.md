---
title: 7. 네트워크 계층(IP)
date: 2021-11-17 20:21:40
tags:
category:
    - Computer Science
    - Network
---

### **a. IPv4 데이터그램 헤더**



![img](https://blog.kakaocdn.net/dn/c2JaDj/btq4qAK2iFT/Kms6bFZUha3wxTzWkBNKR0/img.png)



 

버전 번호 : 4비트. 데이터그램의 ip 프로토콜 버전 명시.

헤더 길이 : ip 데이터그램에서 실제 페이로드가 시작되는 곳을 결정.(데체로 헤더는 20바이트)

서비스 타입 : IPv4의 서비스 타입을 구별(실시간 데이터그램인가 아닌가 등)

데이터그램 길이 : 헤더를 포함한 전체 데이터그램 길이

식별자 , 플래그, 단편화 오프셋 : 단편화에 사용됨.

TTL : 라우터가 데이터그램을 처리할 때마다 감소. TTL 필드 값이 0이 되면 라우터가 데이터그램을 폐기

프로토콜 : 데이터 부분이 전달될 목적지의 전송 계층의 특정 프로토콜을 명시

헤더 체크섬 : 비트 오류를 탐지.

출발지와 목적지 IP 주소

옵션

데이터(페이로드)

 

### **b. IPv4 데이터그램 단편화**

MTU : 링크 계층 프레임이 전달할 수 있는 최대 데이터 양

 

문제 : 송신지와 목적지 간의 경로를 따르는 각 링크가 다른 링크 계층 프로토콜을 사용할 수 있고 각 프로토콜이 서로 다른 MTU를 가질 수 있게 됨.

 

해결 : IP 데이터그램의 페이로드를 두 개 이상의 더 작은 IP 데이터그램으로 분할하고 각자 링크 계층 프레임으로 캡슐화.

이렇게 나눠진 데이터그램을 조각(fragment)라고 부른다.

 

그렇다면 이렇게 나눠진 조각들을 언제 재결합 하는가? => end system에서 데이터그램 재결합!

목적지 호스트가 받은 데이터그램이 어떤 조각인지 파악하려면? => 헤더에 식별자, 플래그, 단편화 오프셋을 활용!

 

#### b-1 식별자, 플래그, 단편화 오프셋

식별자는 단편화된 데이터그램끼리는 같은 값을 지닌다.

플래그는 목적지 호스트가 데이터그램의 마지각 조각을 수신했음을 알리기 위한 장치. 마지막 조각은 0을, 아닌 조각은 1을 가짐.

단편화 오프셋는 조각의 분실방지와 순서대로 재결합을 위한 장치. 원본 데이터그램 내에 조각의 시작 위치를 나타냄.(예시에서는 8비트이므로 원본 위치에서 8을 나눈 값을 입력.)



![img](https://blog.kakaocdn.net/dn/9BuK3/btq4ry61IjP/MikCAMGbNbcZ5Fwb2hNE6k/img.png)



### **c. IPv4 주소체계(서브넷)**



![img](https://blog.kakaocdn.net/dn/cr2k9S/btq4uGXzzxe/G5kBtY7KsYBAIEeFkoykK0/img.png)



인터페이스 = 호스트와 물리적 링크 사이의 경계



![img](https://blog.kakaocdn.net/dn/bEKM5O/btq4vQ6JWsg/tBaxlK7EuylKFMWJYAk2Wk/img.png)



십진 표기법 = 32비트 ip주소를 각 바이트를 십진수로 표현하고 각 바이트를 점으로 구별.

 

모든 호스트와 라우터는 IP 데이터그램을 송수신할 수 있으므로 각 호스트와 라우터 인터페이스가 IP 주소를 갖도록 함.

 



![img](https://blog.kakaocdn.net/dn/I5iii/btq4rzLFeiO/SA3o2o9hU0IVgkcS3i8j9K/img.png)



서브넷 = 인터페이들과 하나의 라우터 인터페이스로 연결된 네트워크

서브넷 마스크 = 32비트 주소 중 서브넷 주소를 가르키는 부분(/24일 경우 왼쪽 24비트가 서브넷 주소임)

네트워크 마스크 = 255.255.255.0 (이 네트워크 마스크와 and 연산을 하게 되어 나타나게 되는 주소가 서브넷)

 

하지만 서브넷은 여러 호스트를 라우터 인터페이스에 연결하는 것만을 의미하진 않음.

각 서브넷은 호스트나 라우터에서 각 인터페이스를 분리하여 생긴 고립된 네트워크를 의미.



![img](https://blog.kakaocdn.net/dn/co6KAT/btq4vQZX18D/4TYywTk6oV0p3fkb40F3c1/img.png)



### **d. 클래스 주소체계**

클래스 주소체계 : ip 주소의 네트워크 부분을 8, 16, 24비트로 제한. 해당 서브넷 주소를 갖는 서브넷을 A, B, C 클래스 네트워크로 분류.



![img](https://blog.kakaocdn.net/dn/bRmVjk/btq4qzk6jiG/w73SKkojmNdWhlFhs7MDN0/img.png)



하지만 서브넷의 주소넷 부족 현상. 호스트 id 낭비 발생.

 

### **e. CIDR**

ip가 십진수 형태의 a.b.c.d/x로 표현. 이때 x는 ip주소의 네트워크 부분을 표시하며 프리픽스라 함.

따라서 x는 네트워크 id

32-x는 호스트 id를 담당.

 

라우터가 CIDR을 찾아갈때 포워드 테이블의 네트워크 마스크와 and 연산을 하여 목적지와 일치하는 prefix가 가장 길게 일치하는 것과 연결.

 

#### e-1 CIDR 경로 집약

여러 네트워크를 알리기 위해 하나의 네트워크 접두사를 사용하는 것.

ex)32비트의 ip 주소를 할당하는 과정을 지켜보자.

 

먼저 8개의 조직을 가진 곳에 ip를 할당해야 된다고 가정하자.

이때 a라는 ISP가 큰 인터넷 연결을 위해 할당받는다. 현재 예시에서는 20비트를 할당받는다 하자.

그러면 우리는 자체 서브넷을 x.y.z.0/20으로 표현 가능하다.

그렇다면 이 조직들이 어떤 ip를 할당 받았는지를 알려야한다.

근데 이를 굳이 일일히 굳이 이 서브넷의 조직이 8개임을 굳이 알릴 필요가 없개된다.

그냥 x.y.z.0/20으로 표현해도 일맥상통한다. 즉 주소가 더 간편해졌다.(경로 집약되었다.)



![img](https://blog.kakaocdn.net/dn/F3Dyb/btq4WK0eFWz/MlSKkoyJDna09GXoFxrQ4k/img.png)Fly-By-Night-ISP가 200.23.16.0/20만 알려도 된다. 



이때 만약 어떤 조직이 다른 ISP로 연결되야 되는 경우는 어떻게 될까?

다른 ISP는 이제 자신의 서브넷 뿐만 아니라 자신에게 옮겨온 조직의 ip도 알려야한다.



![img](https://blog.kakaocdn.net/dn/W0tYy/btq4XyF6iBH/4le2XzK8B8g3otKiih8utK/img.png)



### **f. 특별한 IP주소**

0.0.0.0 = 새로운 호스트가 DHCP 서버에 주소 할당 요청할 때

255.255.255.255= 해당 네트워크의 모든 호스트가 수신. 해당 서브넷에서는 사용. 외부로는 전달 x

127.x.y.z = 네트워크 계층에서 하위로 전달하지 말고 다시 상위 계층으로 보내라는 의미.

사설아이피 : 해당 서브넷에서만 사용되는 아이피

directed broadcast adress : 라우터가 broadcast 해야할 때. 라우터의 네트워크 마지막 주소로 x.y.z.255

network address : 네트워크 블록에서 맨 처음 주소로. x.y.z.0

 

### **g. 동적 호스트 구성 프로토콜 (DHCP)**

자동으로 호스트에 IP 주소를 할당하는 기능.

 

각 서브넷은 DHCP 서버를 갖는다.

DHCP 서버가 없는 서브넷은 DHCP 연결 에이전트(라우터)가 DHCP 서버 주소를 알려줘 DHCP를 이용할 수 있게한다.

UDP 포트넘버 67을 사용



![img](https://blog.kakaocdn.net/dn/Sklt4/btq4XE0nHBN/ihu3O8jB8GKIkEk0d4lcHK/img.png)



 



![img](https://blog.kakaocdn.net/dn/em7oGL/btq4X6CornQ/wbAiJ0ZdDsyyW70bojDvQK/img.png)



DHCP discover : DHCP 서버가 있나요?

-DHCP 발견 메시지를 포함하는 IP 데이터그램을 서브넷에 연결된 모든 노드로 브로드캐스팅

-목적지 IP 주소를 255.255.255.255, 출발지 IP 주소를 0.0.0.0로 설정해서 브로드캐스팅

 

DHCP offer : 제가 DHCP서버입니다! 당신이 쓸 수 있는 IP주소를 드립니다.

-DHCP 제공 메시지를 ip브로드캐스트 주소(255.255.255.255)로 서브넷에 모든 노드로 브로드캐스팅

-메시지에는 발견 메시지의 트랜잭션 ID, 클라이언트에 제공된 IP 주소, 네트워크 마스크, IP 주소 임대 기간을 포함

-여러 DHCP 서버가 ip를 제공할 경우 최적의 위치에 있는 서버를 선택

 

DHCP request : 좋아요 이 주소로 할게요!

-받은 ip주소와 임대 기간을 확인차 전송

 

DHCP ack : 좋아요 이 주소 쓰시면 돼요!

-요청된 파라미터를 확인차 전송

 

### **h. 네트워크 주소 변환 (NAT)**

ip 주소 할당량이 부족해지는 상황이 발생하면 어떻게 해결해야될까?

NAT를 활용하여 사설 ip를 할당하는 것.



![img](https://blog.kakaocdn.net/dn/entwIb/btq4Xx1vklj/kSvvZKnUSXrYwUaonlrnYK/img.png)



NAT 가능한 라우터에 의해 public ip와 사설 ip가 나눠져 있는 걸 알 수 있다.

이때 NAT을 활용하는 라우터는 외부에서는 라우터가 아닌 하나의 IP 주소를 가지는 하나의 장비로 인식된다.

홈 네트워크의 여러 ip주소들은 DHCP로 할당받는다.

 

홈 네트워크 내부에 있는 장비들은 포트번호로 구별한다.

포트번호는 홈네트워크에서 전송할 때 NAT 변환 테이블로 할당한다.



![img](https://blog.kakaocdn.net/dn/77zDL/btq4Ykgn1Pe/OfAYkW42pKhb3OJVxC85AK/img.png)



하지만 외부의 클라가 NAT 안에 있는 서버에 접근할 수 없게 된다.(NAT 변환테이블은 NAT에서 데이터 보낼 때 할당되므로)

\1. NAT 변환 테이블에 필요한 포트를 미리 써놓기? => 유지보수가 힘들어진다.

\2. NAT 변환 테이블에 장치를 자동으로 등록

\3. relay방식. 중간에 전달 서버를 만들어 중간 서버가 NAT 내부 IP와 포트번호 전달.



![img](https://blog.kakaocdn.net/dn/zkXaI/btq4YPmHd0k/kiVLR05TkQLjW5vhg0pgHK/img.png)



### **i. IPv6**

IPv4의 32비트 주소 공간이 빠르게 고갈되어 등장한 128비트 프로토콜

간소한 헤더를 사용해 더 빨리 처리하도록 했다.



![img](https://blog.kakaocdn.net/dn/yYAJD/btq4W03126r/oechgWJhqrCjEHDJLMnOJ0/img.png)



트래픽 클래스 : 특정 응용 프로그램의 우선 순위 부여

흐름 라벨 : 데이터그램 흐름을 인식

다음 헤더 : 전송 프로토콜을 구분 TCP, UDP

홉 제한 : 라우터가 데이터그램을 전달할 때마다 1씩 감소. 0보다 작아지면 데이터그램을 버림

 



![img](https://blog.kakaocdn.net/dn/8CPii/btq4XhdpLFW/hMIWuSAnK0j2nRVKzatSPk/img.png)



IPv4에서 IPv6으로 전환하는 터널링.

IPv6 사이에 있는 IPv4 라우터들을 터널로 칭함.

IPv6의 데이터그램을 IPv4 데이터그램의 필드에 넣는다.

 

### **j. 소프트웨어 기반 네트워크 SDN**

각 패킷 스위치의 검색 추가 작업 테이블을 보여주며 테이블은 원격 컨트롤을 통해 계산, 설치 및 업데이트



![img](https://blog.kakaocdn.net/dn/l2DzE/btq4XfUcism/NYANWo2nScj6yfbMaRYlD1/img.png)



각 라우터마다 가지고 있는 플로우 테이블을 통해 다양한 행동을 취한다.

헤더세트와 일치하는 패캣을 카운터 세트에 업데이트하고 플로우 테이블 요소와 일치하면 행동을 처리



![img](https://blog.kakaocdn.net/dn/H55Cl/btq4XD1Dy1z/aw5TP8o8wCYqcVpppZOuDk/img.png)



match+action : 서로 다른 종류의 장비를 통일

라우터 match = longest IP prefix + action = forward out a link

스위치 match = 목적지 MAC 주소 + action = forward or flood

방화벽 match = ip 주소와 TCP UDP 포트번호 + action = 수용 혹은 거부

NAT match = IP 주소와 포트 + action = 주소와 포트를 재작성
