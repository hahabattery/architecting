---
layout: default
title: Windows
grand_parent: Tools
parent: OS
nav_order: 4
---

# Command Windows

### Command Prompt

 * Windows CMD
```
C:\> set VAR_NAME="VALUE"
```


 * Windows CMD
```
C:\> echo %VAR_NAME%
```

 * multi row command
```
use ^
```

 * 환경변수 보기
```
set
```

### Powershell
 * Windows PowerShell
```
PS C:\> $env:VAR_NAME="VALUE"
```

 * Windows PowerShell
```
PS C:\> $env:VAR_NAME
```

 * Permanently set an environment variable for the current user:
```
C:\> setx VAR_NAME "VALUE"
```

 * Permanently set global environment variable (for all users):
```
C:\> setx /M VAR_NAME "VALUE"
```

 * multi row command
```
use `
```

 * 환경변수 보기
```
Get-ChildItem Env:
```



# port 를 사용하는 프로세스 찾기
```
C:\Users\zetawiki>netstat -ano | findstr :873
  TCP    0.0.0.0:873            0.0.0.0:0              LISTENING       328
  TCP    [::]:873               [::]:0                 LISTENING       328

C:\Users\zetawiki>tasklist | findstr 328
rsync.exe                      328 Services                   0      4,880 K

# Process 죽이기
taskkill /f /pid 328 
```
 * -o 옵션이 process id 표시하는 옵션


# Windows에서 linux 환경 사용하기

### OpenSSH client 설치
 * "메뉴 – 마우스 오른쪽키 클릭 – 앱 및 기능 - 선택적 기능 관리" 에서 기능추가를 천택해서 OpenSSH클라이언트 설치
 

### 개발자 모드 활성화

 * 윈도우 - 설정 - 업데이트 및 보안 - 개발자용 - 개발자 모드 체크 
 * 제어판 - 프로그램 - Window 기능 켜기/끄기 에서 Linux용 window 하위 시스템 체크
 * Microsoft store에서 ubuntu 앱을 설치후 
 * CMD창에서 bash 실행


