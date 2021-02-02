---
toc: true
title: " Jenkins Gitlab 연동. Gitlab Token, WebHook"
description: "Jenkins Gitlab 연동. Gitlab Token, WebHook"
categories: [Jenkins]
tags: [Jenkins]
redirect_from:
  - /2019/05/27/
---

> Jenkins Gitlab 연동. Gitlab Token, WebHook

### Jenkins 에서 Git 관련 플러그인 설치 {#toc1}
```md

Gitlab 관련 플러그인만 설치 해도 되겠으나, 그냥 
Jenkins Server URL http://IP:PORT/pluginManager/available 로 이동
혹은 Jenkins - Jenkins 관리 - 플러그인 관리 - 설치 가능 탭 클릭
필터에 Git 입력 대문자 중요!! 
해서 나오는것들중에 이것저것 맘에 드는거 체크 하고 설치해 보면 된다.

```

### Gitlab Access Token 생성 및 Jenkins에 해당 Token 등록 {#toc2}
```md
Gitlab의 User Settings - Access Tokens에서 생성 가능

Name, Expres at 항목 입력후
Scopes 항목 3개 전부 체크 후,
Create 버튼 클릭

Jenkins 로 넘어와서
Jenkens - Credentials - System - Global credentials - adding some credentials 클릭

Jenkens - Jenkins 관리 - 시스템 설정 - Gitlab 항목에 추가
Connection name 입력
Gitlab host URL 입력
Credentials 에 none 클릭 하면 GitLab API token 이 보일거임 선택
저장


```

### Jenkins Job 생성 {#toc3}
```md

Jenkins - 새로운 Item - Job 이름 입력 후 Freestyle project 클릭 후 OK 버튼 클릭
General 에 GitHub project 항목에 체크
URL 에 Git Repository URL 입력후 GitLab Connection 클릭해서 등록한 Credential 이름 확인 후 클릭

Access Token 으로 하기 싫고 Job (Project)단위로 하려면 해당 Job 클릭 - 구성 클릭 - 소스 코드 관리 의 Git 항목에 
URL 입력. Credentials 정보에 Username & Password 입력하면 됨.

다음으로 빌드 유발 항목에
Build when a change is pushed to GitLab. 체크

Build 항목에 원하는 형태로 작성
일단 연동이 잘되는지를 위해 shell 에 echo "Hello" 입력 추천

빌드 후 조치 항목에
Publish build status to GitLab 선택

저장

```

### Gitlab WebHook 등록 {#toc4}

```md

Gitlab으로 돌아와서
Project - Settings - Integrations 을 클릭하면
URL 항목에 Jenkin URL 입력 (http://IP:PORT/project/yourJobName)
잘 안되면 http://IP:PORT/gitlab/build_now 로도 한번 시도해 보삼.

Trigger 항목에
Push Event 와
Merge Request events 체크

Enable SSL verification 체크. (맘에 안들면 해제)

Test 후 Save Change

Jenkin Build 가 실행되었는지 확인
잘 안되었으면 Jenkin Build 만 실행해서 Console 로그 확인
Jenkin 가 Git Clone 을 잘 했으면 Gitlab 에서 WebHook Test 로그 확인해서 처리

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io