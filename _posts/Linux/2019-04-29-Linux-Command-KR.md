---
toc: true
title: " Linux 명령어 모음(CentOS 위주)"
description: "Linux 명령어 모음"
categories: [Linux]
tags: [Linux]
redirect_from:
  - /2019/04/29/
---

> CentOS 에서 사용하는 명령어를 주로 모아놨습니다.

### process kill {#toc1}

```md
for pid in $(ps -ef | grep "concourse" | grep -v "grep" | awk '{print $2}'); do kill -9 $pid; done
```

### zip 사용법 {#toc2}

```bash
yum install unzip
# mydir 안에 zip 파일 내용을 압축푼다.
unzip test.zip -d ./mydir
# 폴더 구조를 만들지 않고 바로 파일이 현재 경로에 풀린다.
unzip -j test.zip

# 하위 폴더 까지 압축하고 싶을때
zip -r test.zip ./*  

# 파일 리스트만 출력하고 압축은 풀고 싶지 않을때
unzip -Z1 catalina-2021-02-22.zip
```

### ls 파일 이름만 {#toc3}

```bash
find . -name '*.jar' -exec basename {} \;
```

### VIM 에서 String Replace 관련 {#toc4}

```bash
:%s/.*System.out.println.*//gc # c 는 변환대상마다 확인 옵션
# 그룹핑 하려면 VI 에서는 \( \) 형태로 escape 해줘야 그 안의 내용이 그룹화 된다. 일반 regex 랑 다름.
# .*? 첫번째 매칭을 시도해 봤는데 잘 안됨.. 어떻게 해야 하는지 모르겠음.
```

### Disk Size {#toc5}

```bash
df -h
du -sh /path/to/directory
```

### grep after grep {#toc6}

```bash
grep -l word ./* | xargs grep word2
grep -lZ word ./* | xargs -r0 grep word2
```

### Find Command with Options {#toc7}

```bash
# Some of Useful Options that I use often
# -t type, -name filename (Regex Possible)
# -maxdepth 1 (current path only)
# Ex)
find / -type f -name "*.java"
find /home -type d -name "*test"
find . -maxdepth 1 -name "*.txt"
```

### Run Shell command after mathing file found {#toc8}

```bash
# To execute linux command for each files that were found, type "{} \;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} \;
# To execute linux command for all files that were found, type "{} +;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} +;
```

### Find file whose size is over 20Mb {#toc9}

```bash
find /usr -size +20000 -print -exec ls -alt {} +; 
```

### Find file where modified date is over 3 days in current folder level. Print Timestamp Format  {#toc10}

```bash
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n"
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n" -delete
```

### Change access modified timestamp 변경  {#toc11}

```bash
touch -a -m -t 202101280130.09 catalina.out
```

### Check if a directory exists  {#toc12}

```bash
if [ -d "$DIR" ]; then  
  mkdir ${DIR}
fi
```

### Check if a directory exists  {#toc13}

```bash
# 0을 왼쪽에 붙여주는 명령어
$(seq -w 1 12)
Ex) for day in $(seq -w 1 31); do
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
