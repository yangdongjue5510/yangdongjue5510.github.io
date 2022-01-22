---
title: 11. 네트워크 계층 제어 평면(ICMP, SNMP)
date: 2021-11-17 20:27:48
tags:
category:
    - Computer Science
    - Network
---

### **a. 인터넷 제어 메시지 프로토콜(ICMP)**

호스트와 라웉터가 서로 간에 네트워크 계층 정보를 주고받기 위해 사용된다.

대표적으로 인터넷 문제 식별을 할 수 있다.

 

ICMP는 구조적으로 IP 바로 위에 있고 IP 페이로드에 담겨 전송된다.



![img](https://blog.kakaocdn.net/dn/tJVQS/btq650mniSM/7H7sJpW8UMdwLPL8Bk2rB1/img.png)



TYPE과 CODE값으로 상태를 구분해 인식한다.

 

#### **a-1. ICMP 메시지 타입**



![img](https://blog.kakaocdn.net/dn/GqD5n/btq69WDfFMN/QTsmbPz5wjviy7WZ57KEOk/img.png)![img](https://blog.kakaocdn.net/dn/dY6knq/btq66WcGvVA/wUEYRvYDOPHlXIKdiOIRlK/img.png)



 



![img](https://blog.kakaocdn.net/dn/GbYNs/btq66lcISpa/FYxEI6brTuv1WtsbNUct40/img.png)

![img](https://blog.kakaocdn.net/dn/YlR1I/btq69WiWwim/wKzyNBA5RfKNenSM0aT1IK/img.png)



### **b. 네트워크 관리와 SNMP**

적절한 비용으로 요규사항을 만족시키기 위해, 네트워크와 구성요소 자원들을 감시, 테스트, 폴링, 설정, 분석, 평가, 제어하는 하드웨어, 소프트웨어, 인간요소 등을 배치하고 통합, 조정하는 것.



![img](https://blog.kakaocdn.net/dn/p9hpi/btq67SOTdPS/kSTGytZ6NNf2zLRcc9odCk/img.png)



관리 서버는 네트워크 관리 활동이 일어나는 장소. 네트워크 관리 정보의 수집, 처리, 분석, 게시를 제어.

피관리 장치는 관리 대상 네트워크에 존재하는 네트워크 장치들. 피관리 장치 내부에는 피관리 객체들이 있다.

피관리 객체는 하드웨어 일부, 소프트웨어 요소에 대한 설정 매개변수들이다.

피관리 장치 내의 피관리 객체들에 관련된 정보들은 MIB에 저장된다.

피관리 장치에는 네트워크 관리 에이전트가 있어 관리 서버와 통신하며, 피관리 장치에 대한 행동을 취하는 프로세스이다.

네트워크 관리 프로토콜은 관리 서버와 피관리 장치들 사이에서 연결.

 

### **c. SNMP**

관리 서버와 그 관리 서버를 대표하여 실행되고 있는 에이전트 사이에서 네트워크 관리 제어 및 정보 메시지를 전달하기 위해 사용



![img](https://blog.kakaocdn.net/dn/rdnlE/btq7biGi4j2/NKZwko1p5fDOXcu4kGSGqK/img.png)



SNMP 관리 서버는 에이전트에게 요청을 수신하고 SNMP 에이전트는 이를 수행한 후 요청에 대한 응답을 보냄

에이전트가 요구받지 않았더라도 트랩 메시지를 관리 서버에게 전송.

트랩 메시지는 MIB 객체 값들을 변화시킨 예외 상황의 발생을 통지하기 위해 이용.



![img](https://blog.kakaocdn.net/dn/bqDTMm/btq67RJdUss/KvZuvg5faGthGiNdKSMCKK/img.png)



출처: https://nongue.tistory.com/165?category=866237 [가자미의 개발이야기]
