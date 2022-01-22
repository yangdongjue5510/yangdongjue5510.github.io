---
title: 3. 어플리케이션 계층
date: 2021-09-16 21:31:20
tags:
category:
    - Computer Science
    - Network
---
### a. 네트워킹 어플리케이션 제작
서로 다른 엔드시스템에서 네트워크를 통해 소통하며 작동한다.
일반적으로 네트워크에 대해 몰라도 어플리케이션 제작 가능

### b. 클라이언트-서버 구조
서버 : 항상 연결, 영구적 IP주소, 더 많은 양을 처리하기 위해선 데이터 센터 구비
클라이언트 : 간헐적으로 연결, 다이나믹 IP주소 사용(위치에 따라 변경), 직접 통신하진 않음. 통신의 시작

### c. P2P(peer to peer) 구조
항상 연결된 서버가 없는 구조.
엔드시스템끼리 직접 통신 가능
자가확장성(self-scalability) : 더 많은 peer가 참여하면 처리 능력도 높아진다.
peer들이 연결됐다가 해제됐다가 빈번. 관리 어렵고 IP주소가 자주 바뀜

### d. 프로세스
어플리케이션 프로그램이 호스트에서 실행되는 것을 프로세스라 함.
동일한 호스트 내에서 두 프로세스가 소통할 때 : inter-process communication
다른 호스트끼리 프로세스들이 소통할 때 : exchanging messages

### e. 소켓
![](/img/network/network3-1.png)
소켓은 쉽게 API로 이해하자.
소켓은 어플리케이션 하위 계층(네트워크 링크...)를 몰라도 호스트끼리 연결될 수 있게 돕는다.
메세지만 내가 작성하고 나머지 연결하는 일은 소켓이 한다.

### f. 포트넘버
IP는 호스트의 위치를 나타내는 주소라고 했다.
그런데 호스트에도 다양한 프로세스가 존재하는데
내가 받은 메시지가 어떤 프로세스에 줘야하는 지는 포트넘버를 통해 이뤄진다.

### g. 어플리케이션 프로토콜의 역할
헤더의 메시지 타입 정의(request인지 response인지)
메시지의 어디까지를 헤더로 하고 어디까지를 페이로드로 정할지.(필드 지정)
메시지의 정의(100110를 request로 정의하자..)
언제 어떻게 메시지를 보내고 답변하는 규칙 수립

### h. 어플리케이션 프로토콜의 종류
오픈 프로토콜 : RFC에 정의된 프로토콜, 상호연동성 확보, ex.http, smtp
사설 프로토콜 : 서비스만하고 프로토콜은 공개하지 않는 경우  ex.스카이프...

### i. 어플리케이션이 필요로 하는 요소
1\. data integrity : 정확한 메시지 전달해야 하는지, 그 정도는 아닌지를 파악
2\. timing : 딜레이가 낮은 것이 중요한지(실시간 게임)
3\. throughput(bps) : 일정한 속도로 전송받아야 하는지 (스트리밍 서비스)
4\. security

### j. TCP UDP 간단하게 알아보기
TCP는 data integrity를 제공
그 외는 제공하지 않으므로 어플리케이션에서 해결해야 한다.(ex.버퍼링)
UDP는 data integrity를 미제공

### k. SSL
보안을 담당하는 어플리케이션 SSL
수송 계층(TCP, UDP)에서는 보안을 지원하지 않아서 SSL같은 어플리케이션에서 해결해야 한다.

### l. web
web : HTML 베이스, 여러가지 오브젝트 포함
URL을 통해 식별(http://www.someschool.edu(호스트이름)/someDept/pic.gif(패스이름))

### n. http
http : hypertext transfer protocol
TCP를 사용. 포트번호 80번을 사용.
stateless =상태를 기억하지 않음
\=서버는 클라이언트를 기억하지 않는다. 이전에 통신됐던 클라이언트도 기억해내지 못한다.

### m. http 종류
non-persistent HTTP
연결할 때 하나의 오브젝트만 TCP를 통해 전달할 수 있음
여러개의 오브젝트를 전달하려면 여러번 연결을 해야함

persistent HTTP
한 TCP연결로 여러 오브젝트를 보낼 수 있음

### o. http 연결
RTT = 패캣이 클라에서 출발해 서버를 거쳐 다시 클라로 올 때 걸리는 시간.

HTTP 연결하려면..
초기 TCP연결을 위한 최초 RTT
요청 HTTP와 응답 HTTP
2RTT(persist HTTP)
2RTT+file transmission time(non-persist HTTP)

### p. HTTP 요청 메시지
ASCII 형식에 따름.
![](/img/network/network3-2.png)
헤더예시

![](/img/network/network3-3.png)
일반적인 헤더 모양

### q. 업로딩 형식 입력
method 부분에 어떤 것이 오느냐에 따라 달라진다.
POST method : input을 entity body에 넣어서 전달
GET method(URL method) : input을 request line의 URL에 넣어서 전달
(겟 메소드를 사용시 주소창에 ?와 &이 생김)

### r. 메소드 타입
HTTP/1.0 : GET, POST, HEAD
HTTP/1.1 : GET, POST, HEAD, PUT, DELETE

### s. HTTP 응답 메시지
![](/img/network/network3-4.png)

### t. HTTP 응답 상태 코드
첫번 째 줄 두번째 요소를 담당.
OK(200) 요구 전달완료. 요구한 오브젝트가 있음
Moved Permanently(301) 요구한 오브젝트가 이동되었음. 해당 위치를 담았음
Bad Request(400) 서버가 요구 메시지를 이해하지 못했음
Not Found(404) 요구 문서를 서버에서 찾을 수 없음
505 HTTP Version Not Supported