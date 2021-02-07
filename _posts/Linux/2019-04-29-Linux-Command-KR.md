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

### zip {#toc2}
```md
yum install unzip
unzip test.zip -d ./mydir
zip test.zip ./*
zip -r test.zip ./*  

스크립트로 사용시 zip의 r 옵션은 절대경로로 잡은경우에, 압축을 풀면 해당 경로로 압축해제를 시도함
결국 해당 폴더 생성해 놓고 적절한 폴더 위치에서 unzip을 추천

```

### ls 파일 이름만 {#toc3}
```md
find . -name '*.jar' -exec basename {} \;
```

### VIM 에서 String Replace 관련 {#toc4}
```md
:%s/.*System.out.println.*//gc # c 는 변환대상마다 확인 옵션
# 그룹핑 하려면 VI 에서는 \( \) 형태로 escape 해줘야 그 안의 내용이 그룹화 된다. 일반 regex 랑 다름.
# .*? 첫번째 매칭을 시도해 봤는데 잘 안됨.. 어떻게 해야 하는지 모르겠음.
```

### Disk Size {#toc5}
```md
df -h
```

### grep after grep {#toc6}
```md
grep -l word ./* | xargs grep word2
grep -lZ word ./* | xargs -r0 grep word2
```

### Find Command with Options {#toc7}
```md 
# Some of Useful Options that I use often
# -t type, -name filename (Regex Possible)
# -maxdepth 1 (current path only)
# Ex)
find / -type f -name "*.java"
find /home -type d -name "*test"
find . -maxdepth 1 -name "*.txt"
```

### Run Shell command after mathing file found {#toc8}
```md
# To execute linux command for each files that were found, type "{} \;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} \;
# To execute linux command for all files that were found, type "{} +;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} +;
```

### Find file whose size is over 20Mb {#toc9}
```md
find /usr -size +20000 -print -exec ls -alt {} +; 
```

### Find file where modified date is over 3 days in current folder level. Print Timestamp Format  {#toc10}
```md
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n"
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n" -delete
```

### Change access modified timestamp 변경  {#toc11}
```md
touch -a -m -t 202101280130.09 catalina.out
```

### Check if a directory exists  {#toc12}
```md
if [ -d "$DIR" ]; then  
  mkdir ${DIR}
fi
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
