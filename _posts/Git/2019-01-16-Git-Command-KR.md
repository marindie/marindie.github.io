---
toc: true
title: "Version Control System: GIT 명령어 사용법. SourceTree를 활용한 작업시 장점"
description: "기본적인 GIT 명령어 사용법에 대한 간략한 설명 및 SourceTree 에서 제공하는 기능에 대한 편리한 부분을 정리한 글입니다."
categories: [Version Control System]
tags: [Git]
redirect_from:
  - /2019/01/16/
---

> GIT 기본 명령어 사용법 및 SourceTree 에서 제공하는 편리한 기능에 대한 설명을 담고 있습니다.

# Git Remote 저장소 관련 명령어

이해를 위해 git local 저장소의 폴더명을 git_folder 라고 하고 진행하겠습니다.
cd git_folder 로 해당 폴더에 있다고 가정합니다.

# 기본 명령어
1. Git Local Repository 생성
git init

1-1. Git Remote Repository 추가
git remote add origin http://github/user/test

1-2. Git Staging 하기
git add .
git add \*.java   또는  git add *.java
git add Documentation/\*.txt (Documentation  폴더안의 모든 .txt 파일 staging)

1-3. Git Commit 하기
git commit -m "First Commit."

1-4. Git Log 확인
git log
git log -p -2 
p 옵션은 커밋의 diff 를 보여줌. -2 는 최근 2개의 결과만 보여달라는 의미

1-5. Git Remote Repository 에 Commit 하기
git push origin master


2. Git Remote Repository 다운받기.
git clone http://github/user/test

2-1. Git Remote Repository 단축이름과 URL 확인 명령어.
cd git_folder
git remote -v


3. Git 설정하기.
아래의 내용은 설정에 대한 부분인데, 나는 아래의 내용을 config 하지 않고 사용합니다
ssh 연결도 하지 않고 사용중입니다.

git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

ssl 연결 disable 하는 명령어
이걸 사용하는 이유는, CA 인증서 관련 하여 처리가 어려운 경우가 있을 수 있어서 저는 ssl 인증 비활성화 해놓고 사용중입니다..
git config --global http.sslVerify false

4. git 삭제
저는 명령어 보다는 그냥 .git 폴더를 직접 삭제하는 편입니다. 
현재까지 큰 문제는 없었습니다. 하지만, submodule을 사용할때는 명령어로 하라는 대로 처리하고 있습니다.
submodule 삭제 관련해서는 아래의 5-4번 내용을 참고하시기 바랍니다.

# 기본 예제
제가 일반적으로 remote repository 에 직접 작업한 프로젝트를 최초 등록할때의 작업 순서를 요약해 보았습니다.
먼저 github 사이트에서 remote repository 를 만듬. 이때!!! readme.md 파일을 만들지 않고 프로젝트를 생성합니다.
이게 가장 중요한 점인데, 이유는 readme.md 를 만들게 되면 커밋이 발생하게 되고, 이 remote repository 에 
바로 push 하면 readme.md 커밋한 내용이 내 로컬 PC 에 만든 local repository 에는 없기 때문에, 
기존에 작업해 놓은 폴더를 다이렉트로 remote repository 에 연결하기 불편합니다.
해결을 위해서는 새폴더 만들고 git clone http://github/user/test 로 clone 하고 나서,
내가 작업한 내용 다시 복사 해서 넣고 git add -> git commit 을 해야 정상적으로 진행 됩니다.
뭐 하다 보면 그것도 하게 되기는 하는데... 내가 원하는 방식은,
remote 에 만들고 기존에 작업한 최상위 폴더에 git local repository 만들고 연결해서 바로 커밋이 바로 되는게 목적입니다.

정상적으로 project 만 만들었다고 하면(commit 없이)
작업 폴더에 가서
git init
git remote add origin http://github/user/test
git add .
git commit -m "First Commit"
git push origin master 
하면 정상적으로 실행 됩니다. 계정 정보 물어보는거는 입력하시면 됩니다.
push 할때, ssl 관련 에러가 난다면 
git config --global http.sslVerify false
로 https 비활성화 시켜놓고 일단 진행 하는것도 방법입니다.
ssh 연동 관련 키 생성 및 github 에 추가하는 부분은 검색하시면 잘 나와 있습니다.


# 큰 프로젝트 안에 별도로 관리를 하고자 하는 작은 프로젝트를 관리하는 경우
소스 관리중 어떤 프로젝트가 상위 프로젝트 안에 포함되어 있는 경우에,
Ex) maven module 형태로 각각의 maven project 를 git repository 로 관리되고, 
최상위 폴더에서 각 repository를 참조하여 돌아가는 구조의 프로젝트 일때 submodule 을 사용하면 좋은것 같음.

장점은 하위 repository 의 커밋이 부모에 영향을 주지 않는 다는 점입니다.
Git Commit 의 관점에서 child 의 버전이 하위여도 parent 는 여전히 독립적인것 처럼 commit 이 가능합니다.
parent 와 child 의 sync 가 필요하지 않다는 점이 좋아 보이네요. 
parent 에서 git submodule foreach git pull origin master 나 git submodule init, git submodule update 를 나중에 해주기만 하면
소스 동기화는 언제든 가능합니다.


5. git submodule 추가 방법
git submodule add <repository_url> <submodule_dir_path_starting_from_parent>

Ex) 폴더 구조
parent
	/child
		/submod1
git submodule add http://github/user/submod1 child/submod1

5-1. submodule 파일 삭제 없이 git 에서만 삭제 방법
child 폴더를 child_bak 으로 이름 변경
git submodule deinit child/submod1
git rm -r --cache child/submod1 
다시 child_bak 을 child 로 변경
git submodule add 하면 정상적으로 다시 추가 됨.

5-2. parent 에서 submodule 전부다 sync 하는 명령어
parent 입장에서 submodule 들이 에러가 나는 상황이 아니면, 최신 상태가 유지 되지 않아도 관계 없다.
하지만, 어떤 이유에서 최신화를 시켜야 한다면, 아래의 명령어가 가장 편해 보인다.
git submodule foreach git pull origin master (submodule 들의 remote 이름이 전부 origin에 master branch 이어야 가능할 듯.)

5-3. 새로운 폴더에 parent 부터 다시 clone 하는 경우
git clone http://github/user/parent 를 하고나서 submodule 에 가서 보면 내용이 비어 있다.
이때 개별적으로 내용을 받아오는 명령어는 아래와 같다.
git submodule init
git submodule update

또는

git clone --recursive http://github/user/parent

5-4. submodule 작업한 파일은 남겨 놓고 git 설정 정보만 삭제하는
mv subfolder subfolder_tmp
git submodule deinit subfolder
git rm --cached subfolder
mv subfolder_tmp subfolder
git add subfolder

풀어서 설명하자면, submodule 로 잡은 폴더명을 바꾸고 deinit 명령어를 날리고, git rm --cached 로 파일은 남기고 삭제 한다는
명령어를 날리면 됩니다. 자세한 이유는 모르지만 이렇게 따라하니 다시 등록할때 문제가 없었습니다.

가장 특이한 점이자 중요한 점은, submodule 폴더경로는 parent 폴더를 기준으로 작성해야 하며, parent 폴더에서 git submodule
명령어를 먹여야 하는것 같습니다.

# Submodule 기본 예제

위의 Submodule 내용을 다시 한번 설명하는 것입니다.
저의 경우는 대부분 git 설정을 먼저하기 보다는, 작업을 먼저하고 나서 git을 연동하는 편입니다.
git submodule add 를 할때 경험했던 어려움은,
git submodule add url path 를 입력할때, parent 폴더를 기준으로 작성해야 한다는 점이였습니다.
child/submod1/ 이라고 하면 에러가 난다고 합니다. 그래서 저는 ./child/submod1 이라고도 하지 않고 그냥
child/submod1 이라고 명시하니까 잘 되었습니다. window 에서는 더더욱 그래야 겠지요.

Ex) 폴더 구조
parent
	/child
		/submod1
cd parent
git init
git remote add origin http://github/user/parent
git submodule add http://github/user/submod1 child/submod1
만약 위의 명령어 에서 valid repo 가 아니라고 나오면
cd child/submod1
git init
cd ../..
git submodule add http://github/user/submod1 child/submod1
cd child/submod1
git add .
git commit -m "Submodule First Commit"
git push origin master

Submodule repository url 가서 커밋 내용 확인.

감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io