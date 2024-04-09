---
layout: default
title: Pycharm
grand_parent: Tools
parent: Editors
nav_order: 4
---

# PyCharm 설정 내용

### Python Interpreter 설정

1. Settings 에서 Python Interpreter를 선택하는 데서 "show all"을 선택하고
2. 좌측 상단 + 버튼을 클릭해서 "Add Local Interpreter"를 선택하고
3. "Pipenv Environment"를 선택하고 로컬에 설치된 "pipenv" 바이너리를 선택해준다.(이때 pipenv가 설치되지 않은 경우는 "brew install pipenv" 같은 명령으로 설치해주어야 한다)
4. 터미널을 실행해서 "pipenv shell" 명령을 실행해서 가상환경을 세팅한다.
5. "pipenv install {패키지명}" 명령으로 패키지를 설치할 수 있다.

### virtual Environment 설정
pycharm-> preferences -> project -> python interpreter -> 인터프린터 선택에서 show all 클릭!!
new environment 에서 원하는 경로로 설정하고 OK!


* Hide All Windows 
  + Shift + Cmd + F12

* Extend Selection 
  + Alt + Up Arrow 

* Find Action
  +  Cmd + Shift + A


# Jupyter Notebook  
* Ctrl + Enter
  + Run Cell


Scientific Mode 
2023/2 버전으로 roll back하면 된다고 한다.

https://youtrack.jetbrains.com/issue/PY-64570?_gl=1*rsm8fv*_ga*MzAzMzk1OTc0LjE3MTI1ODQ5ODc.*_ga_9J976DJZ68*MTcxMjY2NDg4OC4zLjEuMTcxMjY2NjU2My42MC4wLjA.&_ga=2.178127207.350659778.1712584987-303395974.1712584987

