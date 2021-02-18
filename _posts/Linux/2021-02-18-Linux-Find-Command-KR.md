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

### 사이즈가 20Mb 이상인 파일 검색 {#toc3}

```bash
find /usr -size +20000 -print -exec ls -alt {} +; 
```

### 파일 수정일자가 3일 이상 지난 파일을 현재 폴더 내에서만 검색, 검색된 파일들의 타임스탬프 출력 {#toc4}

```bash
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n"
# 출력후 삭제 -delete 옵션
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n" -delete
```

### 파일 수정일자가 120일 이상 지난 .log 로 끝나는 파일을 삭제 하는 명령어 {#toc6}

```bash
find /was/logs -name  '*.log*' -mtime +120 -printf "%f\n" -exec rm -rf {} +;
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
