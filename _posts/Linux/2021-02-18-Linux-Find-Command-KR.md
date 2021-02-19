---
toc: true
title: "Linux Find 명령어 사용법"
description: "Linux Find 명령어 사용법"
categories: [Linux]
tags: [Find]
redirect_from:
  - /2021/02/18/
---

> Linux Find 명령어 사용법

### Find 기본 명령어 {#toc1}

```bash
# -t 파일 타입 (f 파일, d 폴더)
# -name 옵션 파일명 (정규식 사용 가능, 모든 정규식 syntax 를 지원하는건 아닌듯함)
# -maxdepth 1 (현재 폴더 내에서만 찾는것으로 제한)
find / -type f -name "*.java"
find /home -type d -name "*test"
find . -maxdepth 1 -name "*.txt"
```

### Find 로 찾은 파일들을 대상으로 추가 명령어 실행 예제 {#toc2}

```bash
# 매칭되는 결과에 각각의 명령어를 실행할 경우
# 결과는 다음과 같이 실행됨 
# grep chrome a.txt
# grep chrome b.txt
# grep chrome c.txt
find . -name "*.txt" -exec grep chrome {} \;

# 매칭된 결과를 실행하는 명령어 뒤에 append 식으로 이어서 완성하여 실행하는 경우
# 결과는 다음과 같이 실행됨
# grep chrome a.txt b.txt c.txt
find . -name "*.txt" -exec grep chrome {} \+
```

### Find name 옵션 사용시 정규식 적용 방법 팁 {#toc3}

```bash
# _ underscore 문자는  $VAR_ 변수뒤에 붙으면 변수명으로 인식된다. $VAR"_" 이렇게 써주면됨 "$VAR'_'" 는 될거 같은데 안됨
# 보통 사용방식은 " 나 ' 없이 변수나 이름을 그대로 적고 
# 테스트시 잘 안되는 부분에 대해서 " 로 감싸주면서 테스트 하면 대부분 잘된다
find . -type f -name $HOSTNAME-*"_"$year$month$day*.log
```

### 사이즈가 20Mb 이상인 파일 검색 {#toc4}

```bash
find /usr -size +20000 -print -exec ls -alt {} +; 
```

### 파일 수정일자가 3일 이상 지난 파일을 현재 폴더 내에서만 검색, 검색된 파일들의 타임스탬프 출력 {#toc5}

```bash
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n"
# 출력후 삭제 -delete 옵션
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n" -delete
```

### 파일 수정일자가 120일 이상 지난 .log 로 끝나는 파일을 삭제 하는 명령어 {#toc7}

```bash
# %f\n 은 파일명만 출력한다
find /was/logs -name  '*.log*' -mtime +120 -printf "%f\n" -exec rm -rf {} +;
```

### 파일들을 찾아서 압축하고 싶은경우 {#toc8}

```bash
# 여러개의 파일이 있는 경우, 여러번 출력된다. 
# zip 의 -m 명령어는 압축후 해당 파일을 삭제한다, gzip 은 참고로 기본적으로 압축후 삭제한다
# \+ 명령어의 의미는 전체 대상을 하나의 라인에 appned 한 다음 하나의 명령어 실행으로 처리하는 의미
# 파이프 라인 식의 xargs 도 사용은 할 수 있지만,
# 압축파일 대상의 경로를 사용자가 잘 알고 써야 하기에
# exec 옵션으로 처리하는게 더 좋다고 봄. 
# find 명령어에서 제공하는 옵션이기에 더 최적화 되어 있는듯 
find /home -type f -name *.log -exec zip -m -v /logs/202010/zipFileName.zip {} \+
```

### 파일을 찾아서 파일명을 제외한 폴더 경로만 추출하고 싶은 경우 {#toc9}

```bash
find /home -type f -name *.log -exec dirname {} \;
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
