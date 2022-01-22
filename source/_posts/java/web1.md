---
title: web 개론
date: 2021-07-30 15:02:27
tags:
---

## 웹의 기본 3가지 요소
1. ##### URI

Uniform Resource Identifier. 리소스 식별자
다양한 정보에 접근할 수 있는 정보

2. ##### HTTP

Hypertext Transfer Protocol
어플리케이션 컨트롤
GET, POST, PUT...

3. ##### HTML
Hyper Text Markup Language
XML 바탕으로 한 범용 문서 포맷.
브라우저가 사용자가 볼 수 있는 형태로 표현.

## REST(자원의 상태 전달) - 네트워크 아키텍처

1. Client, Server : 클라이언트와 서버가 서로 독립적이고 분리

2. Stateless : 요청에 대해서 클라이언트의 상태를 서버에 저장하지 않음

3. Cache : 클라이언트는 서버 응답을 임시저장(cache로)할 수 있어야 함.
*클라이언트가 캐시를 통해 응답 재사용이 가능해야 하고, 이를 통해 서버 부하를 낮춰야 함*

4. 계층화 : 서버와 클라이언트 사이에, 방화벽, 게이트웨이, 프록시 등 다양한 계층 형태로 구성되어야 함.

5. 인터페이스 일관성 : 인터페이스가 일관성 있어야 하고, 아키텍처를 단순화시켜 작은 단위로 분리하여 클라이언트, 서버가 독립적으로 개선할 수 있어야 한다.

6. Code on Demand(optional) : 특정한 기능을 서버로부터 클라이언트가 받아 코드를 실행할 수 있어야 한다.

> 인터페이스의 일관성을 잘 지켰느냐에 따라 REST를 준수했는지 판단
1. 자원 식별
리소스에 접근할 때 사용되는 URI에 자원을 식별하는 내용이 있어야 한다.

2. 메시지를 통한 리소스 조작
리소스를 조작할 때, 데이터 전체를 전달하지 않고, 메시지로 전달해야 한다.

3. 자기 서술적 메시지
요청하는 데이터가 어떻게 처리되야하는지 충분한 내용을 드러내야한다.

4. 애플리케이션 상태에 대한 엔진으로써 하이퍼미디어
REST API를 개발할 때 단순히 클라이언트 요청에 대한 데이터만 응답하는게 아닌, 관련된 리소스에 대한 링크 정보도 같이 포함해야 한다.

## URI

> URI vs URL
**Identifier vs Locator**
URI : 인터넷 특정 자원을 나타내는 주소 값. 해당 값은 유일(응답은 달라질 수 있음)
URL : 인터넷 상에서의 자원, 특정 파일이 어디에 위치하는지 식별하는 주소
**URL은 URI의 하위 개념**

### URI 설계 원칙(RFC-3986)
- 슬래시 구분자(/)는 계층 관계를 나타날 때 사용
- URI 마지막 문자로 슬래스를 포함하지 않음
- 하이픈(-)은 가독성을 위해 사용
- 밑줄(_)은 사용하지 않음.
- 세션 ID를 포함하지 않음
- 메소드 명을 이용하지 않음
- 단수형보다 복수형 사용
- 컨트롤러 이름으로는 동사를 사용
- 경로 부분 중 변하는 부분은 유일한 값을 대체
- CRUD 기능을 나타내는 것은 URI에 사용하지 않는다.
- URI Query Parameter 디자인(컬렉션의 결과 필터링 등)
- 서브 도메인은 일관성 있게 사용
- 클라이언트 개발자 포탈 서브 도메인을 일관성 있게 만듬

### HTTP Protocol
web에서 데이터를 주고 받는 프로토콜.
하이퍼텍스트 전송용 프로토콜로 정의되어 있지만 실제로는 JSON, XML, Image 등 여러 가지를 전송!
TCP 기반으로 한 REST 특징을 모두 구현한 web기반 프로토콜!

HTTP는 요청을 주고 받는 (Request, Response) 형태의 통신 방법!

#### HTTP 메소드
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style12"><tbody><tr><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">의미</td><td style="width: 8.31395%;">CRUD</td><td style="width: 16.686%;">멱등성(몇번을 요청해도 같은 응답)</td><td style="width: 12.5%;">안정성</td><td style="width: 12.5%;">Path Variable</td><td style="width: 12.5%;">Query Parameter</td><td style="width: 12.5%;">DataBody</td></tr><tr><td style="width: 12.5%;">GET</td><td style="width: 12.5%;">리소스 취득</td><td style="width: 8.31395%;">R</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">x</td></tr><tr><td style="width: 12.5%;">POST</td><td style="width: 12.5%;">리소스 생성, 취득</td><td style="width: 8.31395%;">C</td><td style="width: 16.686%;">x</td><td style="width: 12.5%;">x</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">?</td><td style="width: 12.5%;">o</td></tr><tr><td style="width: 12.5%;">PUT</td><td style="width: 12.5%;">리소스 갱신, 생성</td><td style="width: 8.31395%;">C / U</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">x</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">?</td><td style="width: 12.5%;">o</td></tr><tr><td style="width: 12.5%;">DELETE</td><td style="width: 12.5%;">리소스 삭제</td><td style="width: 8.31395%;">D</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">x</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">x</td></tr><tr><td style="width: 12.5%;">HEAD</td><td style="width: 12.5%;">해더 데이터 취득</td><td style="width: 8.31395%;">&nbsp;</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">o</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td></tr><tr><td style="width: 12.5%;">OPTIONS</td><td style="width: 12.5%;">지원하는 메소드 취득</td><td style="width: 8.31395%;">&nbsp;</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td></tr><tr><td style="width: 12.5%;">TRACE</td><td style="width: 12.5%;">요청메시지 반환</td><td style="width: 8.31395%;">&nbsp;</td><td style="width: 16.686%;">o</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td></tr><tr><td style="width: 12.5%;">CONNECT</td><td style="width: 12.5%;">프록시 동작이 터널 접속으로 변경</td><td style="width: 8.31395%;">&nbsp;</td><td style="width: 16.686%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td><td style="width: 12.5%;">&nbsp;</td></tr></tbody></table>

*멱등성(여러번 요청한 값이 한번 요청한 값과 같음), 안정성(요청할 때 리소스에 영향을 주지 않음)