---
title: 7. 프로세스 상태와 스케줄러 알고리즘의 관계
date: 2021-08-04 17:19:51
tags:
category:
    - Computer Science
    - OS
---
## 멀티 프로그래밍과 wait
wait : 프로세스가 실행 중임에도 불구하고 cpu를 사용하지 않고 기다리는 시간
멀티 프로그래밍은 wait 때문에 발생하는 비효율을 줄이는 스케쥴링 알고리즘!
![](/img/os/os7-1.png)
그렇다면 이렇게 효율적으로 하기 위해서는 어떻게 해야할까?
어떤 시점에 어떤 프로세스를 실행시켜야 될지 판단해야 하는데..

이때 중요한게 프로세스 상태다!
===

## 프로세스 상태
![](/img/os/os7-2.png)
- **running state** : 현재 cpu의 실행하고 있는 상태
- **ready state** : cpu 실행 가능 상태(대기 상태)
- **block state** : 특정 이벤트 발생 대기 상태(wait 상태)
- **exit state** : 프로세스가 활용한 리소스를 해제하고 종료하는 상태

![](/img/os/os7-3.png)
1. 특정 이벤트를 대기(block)
4. 특정 이벤트를 확인후 준비 상태로 변환(ready) 
2. 스케쥴려가 해당 프로세스를 선택해 실행 상태로 전환(running)
3. 특정 시간동안 실행하고 다른 프로세스를 실행시키기 위해 다시 준비 상태로 전환(ready)

하지면 여전히 CPU가 어떤 프로세스를 실행시켜야 할 때 상태 정보만으로는 부족하다!


## state queue 도입
이를 위해 state queue를 도입한다.
**ready state queue, running state queue, block state queue**를 만들어서


한 단위 시간에 많은 요청이 왔을 때, 이 요청들을 **ready state queue**에 넣고,
그 중 먼저 하나를 빼와서 **running state queue**에 넣는다.

하지만 어떤 프로세스가 한 단위 시간에 다 끝나지 않는 경우가 있다.
그렇다면 그 프로세스는 running 큐에 들어가고 나서 또 다시 ready 큐에 새롭게 삽입된다.
(시식코너에서 한입하고 다시 줄서는 느낌..)

반면, running 도중, wait 상태에 들어간 프로세스는 **block queue**로 따로 빼서 관리한다.
block에 있던 프로세스는 해당 wait이 끝나면, 다시 ready queue로 들어간다.

만약 프로세스들이 완료되거나, block 상태에 있다면, cpu는 ready queue에서 pop할 수 없다.
이 상태를 **cpu idle** 상태라고 한다!.

> state queue 대신 우선순위 기반으로 할 경우 FIFO가 아닌 다른 방식(프로세스 간 우선순위에 따라)으로 진행될 수 있다.