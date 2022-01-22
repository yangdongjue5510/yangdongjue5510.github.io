---
title: mysql PID에러(The server quit without updating PID file)
date: 2021-10-21 23:29:08
tags: troubleShooting
category:
    - Computer Science
    - Database
---

## mysql이 갑자기 에러가 났다...

주요 증상은 다음과 같다.

```mysql
mysql.server start
Starting MySQL
.. ERROR! The server quit without updating PID file (/usr/local/var/mysql/MUHN2-031.local.pid).
```

```mysql
mysqld start
2021-10-21T13:40:56.896075Z 0 [ERROR] [MY-010273] [Server] Could not create unix socket lock file /var/lib/mysql/mysql.sock.lock.
2021-10-21T13:40:56.896105Z 0 [ERROR] [MY-010268] [Server] Unable to setup unix socket lock file.
2021-10-21T13:40:56.896439Z 0 [ERROR] [MY-010119] [Server] Aborting
2021-10-21T13:40:57.857715Z 0 [System] [MY-010910] [Server] /usr/local/Cellar/mysql/8.0.27/bin/mysqld: Shutdown complete (mysqld 8.0.27)  Homebrew.
```

PID에러가 발생해서 별의 별 짓을 다해봤다.

- 프로세스 인스턴스 종료하기
  - 애초에 프로세스가 실행되지도 않았음
- 디렉토리 권한 설정하기
  - 효과 없음
- 필요한 디렉토리를 직접 만들어주기
  - 효과 없음
- 재설치
  - 효과 없음

이렇게 난항을 겪는 와중 재설치 했을 때 로그를 읽어보니 해답이 보였다.

```mysql
A "/etc/my.cnf" from another install may interfere with a Homebrew-built
server starting up correctly.
```

즉 예전에 설치했을 때 생긴 cnf가 있으면 서버 구동에 문제를 일으킬 수 있다는 안내문이다.

그래서 sudo rm /etc/my.cnf 해줬더니 작동됐다....
