---
toc: true
title: " GIT 명령어 사용법"
description: "기본적인 GIT 명령어 사용법에 대한 간략한 설명을 정리한 글입니다."
categories: [Git]
tags: [Git]
redirect_from:
  - /2019/03/21/
---

> 기본적인 GIT 명령어 사용법에 대한 간략한 설명을 정리한 글입니다.

### 배경 {#a}

이해를 위해 git local 저장소의 폴더명을 git_folder 라고 하고 진행하겠습니다.
cd git_folder 로 해당 폴더에 있다고 가정합니다.

### Git 기본 명령어 {#toc1}

```bash
# local repository 생성
git init

# remote repository 추가
git remote add origin GIT_URL

# Git Staging 하기 
git add .
git add \*.java   #또는  
git add *.java
git add Documentation/\*.txt #(Documentation  폴더안의 모든 .txt 파일 staging)

# Git Commit 하기 
git commit -m "First Commit."

# Git Log 확인
git log
git log -p -2 
#p 옵션은 커밋의 diff 를 보여줌. -2 는 최근 2개의 결과만 보여달라는 의미

# Git Remote Repository 에 Commit 하기 
git push origin master

# Git Remote Repository 내려받기(clone)
git clone GIT_URL

# Git Remote Repository 단축이름과 URL 확인 명령어
cd git_folder
git remote -v
```

### Git 설정하기. {#toc2}

```bash
#아래의 내용은 설정에 대한 부분인데, 나는 아래의 내용을 config 하지 않고 사용합니다
#ssh 연결도 하지 않고 사용중입니다.

git config --global user.name "John Doe"
git config --global user.email johndoe@example.com

#ssl 연결 disable 하는 명령어
#이걸 사용하는 이유는, CA 인증서 관련 하여 처리가 어려운 경우가 있을 수 있어서 저는 ssl 인증 비활성화 해놓고 사용중입니다..
git config --global http.sslVerify false
```

### git pull to, push from, remote master 명령어 {#toc3}

```bash
# git pull 명령어
git pull origin master
git pull origin/branch_name local_branch_name

# merge 명령어
# origin/master 의 remote 브랜치와
# 현재 있는 branch 간의 동기화
# 머지의 결과는 양쪽 브랜치 모두 같은 내용으로 싱크가 마춰진다는 개념으로 이해해야한다
git merge origin/master 

# Remote 브랜치의 Head 버전으로 엎어치려는 경우
git pull --rebase origin/remote_branch_name local_branch_name
git pull --rebase origin master
# 또는 
git fetch --all
git reset --hard origin/master

# 이후에 다시 푸쉬
git push origin master
git push origin/remote_branch_name local_branch_name
```

### git checkout, fetch, branch 명령어 {#toc4}

```bash
# head 커밋의 내용으로 해당 파일을 엎어침
git checkout -- filename
git checkout head filename

# head가 아닌 특정 커밋 버전의 파일 내용으로 덮어씌울때
git checkout commit-id filename

# 새로운 브랜치 생성시, remote 의 특정 브랜치의 head 커밋 버전을 복사해서 생성
# 종종 브랜치 이동시, 충돌이슈를 겪는데, 
# git stash 명령어로 로컬 수정본들을 저장하면 문제가 없다 아래의 git stash 설명도 참고하시길
git checkout -b local-branch-name remote-branch-name(origin/master)
git checkout -b branch01 origin/branch01

# remote branch 정보 갱신
git fetch origin

# 모든 브랜치 정보 보기
git branch -v -a
```

### git merge 명령어 {#toc5}

```bash
# 현재 있는 브랜치가 master 라고 가정
# merge with another branch
git merge branch-name

# 충돌이 났다고 가정 Ex. staging|MERGING 상태
# master 버전으로 업데이트
git checkout --ours file.js
# branch-name 으로 업데이트
git checkout --theirs file.js
# 하고 나서는 add 해주기
git add file.js
# 머지 진행 요청. 재검토 요청. 충돌이 없으면 머지 완료됨
git merge --continue
```

### add commit rollback 취소 {#toc5}

```js
// add 를 rollback. 취소
git reset HEAD CONTRIBUTING.md
// add 한 전체 파일 rollback 
git reset

// commit을 취소하고 해당 파일들은 staged 상태로 워킹 디렉터리에 보존
git reset --soft HEAD^
// commit을 취소하고 해당 파일들은 unstaged 상태로 워킹 디렉터리에 보존
git reset --mixed HEAD^ // 기본 옵션
git reset HEAD^ // 위와 동일
git reset HEAD~2 // 마지막 2개의 commit 취소
```

### git http basic access denied {#toc6}

```bash
# 해당 git 에 계정이 없거나, 계정 정보가 변경되어 있는데 일치 하지 않거나 등등. 
# authentication failed
git remote -v
git remote remove origin #origin 이 아닐 수 있음
git remote add origin http-url-of-your-git-project

# 또 다른 방법
git config --system --unset credential.helper
```

### git stash {#toc7}

```bash
# 현재 로컬에서 사용해야 하는 파일들을 저장하고 다른 브랜치로 넘어갈때
# 혹은 다른 브랜치로 넘어가려는데, 설정 파일 충돌이 나서 문제가 될때, 현재 변경된 파일들을 모두 임시저장 해줍니다.
git stash 

# 저장 내역 확인
git stash list
# stash 내용 확인
git stash show 1 (index 0,1,2 ...)
# stash 내용의 파일들만 보고 싶을때
git stash show 1 --name-only

# 저장된 내용 불러오기 (가장 최근)
git stash apply

# 원하는 목록에서 index 번호로
git stash apply indexNumber

# git stash 를 했는데, 충돌이 나는경우, stash 버전으로 강제로 덮어쓰기
git checkout stash -- .
# 파일을 하나씩 불러들일때
git checkout stash -- filepath 
# 가장 최근이 아닌 다른 stash 정보에서 파일 정보를 사용할때
git checkout stash@{1} -- filepath  

# 머지하면서 처리하고 싶을때
git merge --squash --strategy-option=theirs stash

# 마지막으로 저장한 stash 제거
git stash drop

# 이름으로 삭제를 원할경우 index 번호로 삭제
git stash drop 1

# stash 내용 불러오고 해당 stash 삭제시
git stash pop

# 필요한 파일만 개별적으로 선별해서 git stash 할 수 있도록 사용자 입력을 받음
git stash save "commit message" -p

# 위 명령어를 날리면 잠시후에 user input 형태로 각 diff 별로 어떻게 처리할 지 물어본다
# 그에 대한 답변 옵션 정보는 아래와 같음
# 난 파일 단위로 처리 하고 싶어서 a (all), d (discard) 의미의 두 옵션만 사용해서 처리한다.
   y - stage this hunk
   n - do not stage this hunk
   q - quit; do not stage this hunk or any of the remaining ones
   a - stage this hunk and all later hunks in the file
   d - do not stage this hunk or any of the later hunks in the file
   g - select a hunk to go to
   / - search for a hunk matching the given regex
   j - leave this hunk undecided, see next undecided hunk
   J - leave this hunk undecided, see next hunk
   k - leave this hunk undecided, see previous undecided hunk
   K - leave this hunk undecided, see previous hunk
   s - split the current hunk into smaller hunks
   e - manually edit the current hunk
   ? - print help

# stash 적용한거 롤백
git stash show -p | git apply -R # 가장 최근
git stash show -p 1 | git apply -R # 다른 stash 이면 index 도 뒤에 추가

# stash rollback alias 생성
git config --global alias.stash-rollback '!git stash show -p | git apply -R'
```

### 기본 예제 {#toc8}

```bash
정상적으로 project 만 만들었다고 하면(commit 없이)
작업 폴더에 가서
git init
git remote add origin GIT_URL
git add .
git commit -m "First Commit"
git push origin master 
하면 정상적으로 실행 됩니다. 계정 정보 물어보는거는 입력하시면 됩니다.
push 할때, ssl 관련 에러가 난다면 
git config --global http.sslVerify false
로 https 비활성화 시켜놓고 일단 진행 하는것도 방법입니다.
ssh 연동 관련 키 생성 및 github 에 추가하는 부분은 검색하시면 잘 나와 있습니다.
```

### 큰 프로젝트 안에 별도로 관리를 하고자 하는 작은 프로젝트를 관리하는 경우 {#toc7}

```md
소스 관리중 어떤 프로젝트가 상위 프로젝트 안에 포함되어 있는 경우에,
Ex) maven module 형태로 각각의 maven project 를 git repository 로 관리되고, 
최상위 폴더에서 각 repository를 참조하여 돌아가는 구조의 프로젝트 일때 submodule 을 사용하면 좋은것 같음.

장점은 하위 repository 의 커밋이 부모에 영향을 주지 않는 다는 점입니다.
Git Commit 의 관점에서 child 의 버전이 하위여도 parent 는 여전히 독립적인것 처럼 commit 이 가능합니다.
parent 와 child 의 sync 가 필요하지 않다는 점이 좋아 보이네요. 
parent 에서 git submodule foreach git pull origin master 나 git submodule init, git submodule update 를 나중에 해주기만 하면
소스 동기화는 언제든 가능합니다.
제가 실제로 사용하면서 할때는 , child submodule 먼저 commit 치고, parent 가서 git add . 하고 commit, push 하는 방식입니다.
그냥 둘다 최신화 시키고 있습니다.
```

### git submodule 추가 방법 {#toc8}

```md
git submodule add <repository_url> <submodule_dir_path_starting_from_parent>

Ex) 폴더 구조
parent
  /child
    /submod1
git submodule add GIT_URL child/submod1
```

### submodule 파일 삭제 없이 git 에서만 삭제 방법 {#toc9}

```bash
# child 폴더를 child_bak 으로 이름 변경
git submodule deinit child/submod1
git rm -r --cache child/submod1 
# 다시 child_bak 을 child 로 변경
git submodule add # 하면 정상적으로 다시 추가 됨.
```

### parent 에서 submodule 전부다 sync 하는 명령어 {#toc10}

```bash
# parent 입장에서 submodule 들이 에러가 나는 상황이 아니면, 최신 상태가 유지 되지 않아도 관계 없다.
# 하지만, 어떤 이유에서 최신화를 시켜야 한다면, 아래의 명령어가 가장 편해 보인다.
git submodule foreach git pull origin master (submodule 들의 remote 이름이 전부 origin에 master branch 이어야 가능할 듯.)
```

### 새로운 폴더에 parent 부터 다시 clone 하는 경우 {#toc11}

```bash
git clone GIT_URL 를 하고나서 submodule 에 가서 보면 내용이 비어 있다.
# 이때 개별적으로 내용을 받아오는 명령어는 아래와 같다.
git submodule init
git submodule update
# 또는
git clone --recursive GIT_URL
```

### submodule 작업한 파일은 남겨 놓고 git 설정 정보만 삭제 {#toc12}

```bash
mv subfolder subfolder_tmp
git submodule deinit subfolder
git rm --cached subfolder
mv subfolder_tmp subfolder
git add subfolder

# 풀어서 설명하자면, submodule 로 잡은 폴더명을 바꾸고 deinit 명령어를 날리고, git rm --cached 로 파일은 남기고 삭제 한다는
# 명령어를 날리면 됩니다. 자세한 이유는 모르지만 이렇게 따라하니 다시 등록할때 문제가 없었습니다.

# 가장 특이한 점이자 중요한 점은, submodule 폴더경로는 parent 폴더를 기준으로 작성해야 하며, parent 폴더에서 git submodule
# 명령어를 먹여야 하는것 같습니다.
```

### Submodule 기본 예제 {#toc13}

```bash
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
git remote add origin PARENT_GIT_URL
git submodule add SUBMODULE_GIT_URL child/submod1
만약 위의 명령어 에서 valid repo 가 아니라고 나오면
cd child/submod1
git init
cd ../..
git submodule add SUBMODULE_GIT_URL child/submod1
cd child/submod1
git add .
git commit -m "Submodule First Commit"
git push origin master

Submodule repository url 가서 커밋 내용 확인.
```

### unable to rewind rpc post data - try increasing http.postBuffer {#toc14}

```js
git config --global http.postBuffer 2097152000
git config --global https.postBuffer 2097152000
//(2097152000byte == 2000mb)
```

### git 삭제 {#toc16}

```bash
#저는 명령어 보다는 그냥 .git 폴더를 직접 삭제하는 편입니다. 
#현재까지 큰 문제는 없었습니다. 하지만, submodule을 사용할때는 명령어로 하라는 대로 처리하고 있습니다.
#submodule 삭제 관련해서는 아래의 5-4번 내용을 참고하시기 바랍니다.
```

감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
