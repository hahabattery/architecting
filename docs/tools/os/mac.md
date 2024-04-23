---
layout: default
title: Mac
grand_parent: Tools
parent: OS
nav_order: 4
---

 * Enter Full Screen
   + Cmd + Ctrl + F
   + press again (exit full screen)
* capslock 이 한/영 처리인데 길게누르면 원래 capslock 기능으로 동작한다

# Desktop 

* control + up mission control 표시
* ctrol + 숫자 나 좌우 방향키 로 데스크탑을 아동 가능
* mission control이 표시 될 때, 프로그럄들을 이동 시킬 수 있음
* 분할화면을 이용하면, 이것도 새로운 데스크탑이 만들어지는 방식이다
* 설정에서 자동으로 미션컨트롤 관련해서 설정할 수 있는데, 이것도 동작에 영향을 많이 준다
* 스테이지 매니저 항상 표시되게 설정하는 것이 편할 수도 있다.
* 설정>손쉬운사용>트랙패즈 옵션에서 세선가락 드래그를 살정할 수 있다. 이렇게하면 미션칸트롤과 앱 익스포즈는 네 손가락으로 변경됨



# Chrome hard refresh
'Command + Shift + R' 이나 'Shift + Reload button'


# atom
 * 간단하게 편집하려고 할 때, 해당 디렉토리로 이동해서 명령을 실행할 수 있다.
 * Atom > Install shell commands 를 클릭해서 설치해야함
```
atom .
```

# Port PID 확인하기
 * netstat -nav |grep {포트}

# Finder
* 숨김파일 표시 Cmd + Shift + .
* 파일 확장자 표시 Finder > 설정 > 모든 확장자 표시 <- 체크

# 복붙

 * 복사 Command + C
 * 붙여넣기 Command + V
 * cmd + opt + shift + v 스타일까지 복사

# delete
 * 윈도우의 delete키 fn + <-

# 화면 관련

### 화면 사이즈
 * app’s Window menu (Hold ⎇ for more options)
 * 전체 화면
   + Ctrl + Cmd + F
   + (fn) F    <= 전체화면에서 나오기   


### 화면 이동
 * 커서 안따라오고 화면만 이동
 * Page Up   =  fn + ↑
 * Page Down  =  fn + ↓
 * Home  =  fn + ←
 * End  =  fn + →

 * 커서를 따라오게 하려면
 * Page Up  =  fn + option + ↑
 * Page Down  =  fn + option + ↓
 * Home =  Command + ↑
 * End =  Command + ↓


# SSH Tunneling

 * bastion host 접속
```
ssh -i keyfilename.pem ubuntu@{SSH Bastion Host 주소} -p 22 -N -L 9999:{외부와 격리된 호스트 주소}:22
```

 * private 자원에 접속
```
ssh -i keyfilename.pem ubuntu@127.0.0.1 -p 9999
```
