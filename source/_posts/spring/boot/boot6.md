---
title: 6. intelliJ의 Spring boot 프로젝트에서 JSP파일 생성이 안될 때 해결 방법
date: 2021-09-07 19:59:06
tags: troubleShooting
category:
    - Spring
    - Boot
---
자 intellJ로 Spring Boot 프로젝트를 다루는 와중 문제가 생겼다.
JSP를 추가하기 위해 디렉토리를 만들었는데...
![](/img/boot/boot6-1.png)
여기 WEB-INF 폴더에서 new를 해도 JSP파일을 고를 수가 없다..!


그래서 혼신의 구글링을 통해 해결방법을 찾았다.
![](/img/boot/boot6-2.png)
File - Project Structure - Web Resource Directory에서 우리가 만든 디렉토리 경로를 추가해주면 된다!
wow!
[참고링크](https://stackoverflow.com/questions/44478620/jsp-with-intellij)