---
layout: post
title:  "Github SSH 설정"
date:   2023-09-17 14:30:00 +0900
category: dev
tags: [github, git, ssh]
published: false
---

사람들과 [깃허브](https://github.com/)를 이용하여 협업 할때  

개인 컴퓨터/노트북에서 private github 저장소에 접근하려면  

그에 맞는 권한이 있는 사람들만 접근이 가능하다.  

실제 사람들이랑 협업을 할때 private repository를 생성하여,  

이 repository에 내가 만든 코드를 push 하면서 작업을 하게 된다.  

일반적으로 public repository 같은 경우에는 terminal 을 열고,  

git clone ${CLONE 하고자 하는 깃허브 주소}만 입력하면 현재 터미널의 working directory에  

public repository가 clone 이 된다.  

그러나 private repository를 clone 하려면 그에 맞는 설정 이 필요하다.

이번 문서는 깃허브 계정에 연결된 private repository를 terminal로 쉽게 git clone, git push 와 같이,  

git 명령어를 이용하여 private repository에 접근 하기 위한 설정을 다루도록 한다.  

현재 이문서를 작성하는것은, 주말에 세종집에 내려 왔다가, 버스를 기다리는 동안 작성 하는 중이다.

---

### 1. SSH 키 생성



