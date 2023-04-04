---
published: false
title:  "[Linux] 원격 서버의 localhost(127.0.0.1)를 로컬 환경에서 접속하기"
excerpt: "원격 서버에서 Firefox로 localhost 접속하지 않고, 로컬 서버의 localhost에서 바로 접속하기"

categories:
  - debug
tags:
  - [localhost, remote, tensorboard]

comments: true
 
date: 2023-02-07
last_modified_at: 2023-02-07 
---
 
Tensorboard를 원격 서버에서 띄우면, localhost로 접속해야 한다. 그런데, 원격 서버에서 firefox 같은 창을 띄워서 localhost 까지 들어가는 것이 너무 번거로워서 로컬 윈도우에서 바로 접속하는 방법을 찾아보았다. 

----

접속 끊기지 않게