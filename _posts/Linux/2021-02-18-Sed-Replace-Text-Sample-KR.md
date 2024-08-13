---
toc: true
title: "Find 와 Sed 를 사용한 문자열 또는 텍스트 검색(search) 치환(replace) 명령어"
description: "Sed 를 사용한 문자열 치환 관련 내용"
categories: [Linux]
tags: [Sed]
redirect_from:
  - /2021/02/18/
---

> Sed 를 사용한 문자열 치환 관련 내용

### 문자열 치환(replace) 일반적인 사용방법 {#toc1}

```bash
# 파일 내에서 매칭되는 문자열을 / 이하의 내용으로 변환
sed "s/my target text/my replaced text/" your_file.txt 
```

### 문자열 변경,치환(replace) 대상 목록을 보고 싶을때 {#toc2}

```bash
# -n option 자동 출력을 비활성화. 그렇다고 해서 일치하는 라인을 출력해주는 것도 아님.
# -p option 이 있어야 일치하는 라인을 출력해줌
# 테스트 용도로 활용도가 높음 특히 정규식 사용시
sed -n "s/my target text/my replaced text/p" your_file.txt 
```

### 정규식을 사용한 문자열 치환 {#toc3}

```bash
# 정규식 사용은 -r option 을 사용하면 됨
# 이 예제는, 그룹핑 '( )' 과 OR 조건을 사용 (파이프라인 |)

# Sed OR 조건 예제
sed -n -r "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRED/I DO NOT WANT SEGMENT SYNTAX/p" your_file.txt 

# Sed 그룹핑 예제 \1 은 치환(replace)시, 그룹으로 매칭된 내용을 print 해줌
sed -n -r "s/(SEGMENT) CREATE IMMEDIATE/\1 DROP IMMEDIATE/p" your_file.txt
```

### 대상 파일의 내용을 범위로 하여 문자열 치환 하는 경우 {#toc4}

```bash
# Sed 로 어떤 명령어를 사용할지 n p 옵션으로 확인하였다면, 실제로 치환 명령어를 사용해 보자

sed -i'' -r -e "s/SEGMENT CREATE IMMEDIATE|SEGMENT CREATE DEFERRED/I DO NOT WANT SEGMENT SYNTAX/" your_file.txt 
# p 옵션이 빠져있는지 확인
# -i 옵션이 실제로 치환을 하도록 하는 명령어임
# '' 를 추가한 이유는 sed가 새로운 파일을 만들어서 추가하였음. 추가하니 새로운 파일 생성 안함
# sed -ir -e "s/a/b/" a.txt 라고 명령을 날리면, a.txtr 라는 파일이 새로 생성됨
# -i 옵션은 파라미터를 받기를 기대하기 때문. 백업 파일명임
# 난 그런거 싫어서 늘 '' 추가함
# 그리고 여러 옵션을 묶어서 사용하지 않는것도 중요함 -ir
```

### Find 명령어와 Sed 를 사용하면 좀 더 사용 범위가 확대 된다 {#toc5}

```md
# 한개 이상의 파일들을 대상으로 일괄 치환이 필요할 때
find . -type f -name my_*.log -exec sed -i'' -e "s/foo/bar/" {} \;
```

- find 관련 명령어가 궁금하다면 클릭 [Find 명령어](https://marindie.github.io/linux/Linux-Find-Command-KR)

### 대소문자 무시, 중복허용 (Ignore Case, Global) {#toc6}

```md
# 대소문자 무시 i 옵션
sed -i'' -e "s/foo/bar/i" your_file.txt

# 중복허용 g 옵션
sed -i'' -e "s/foo/bar/g" your_file.txt
```

- Sed 를 사용하여 텍스트를 검색 매칭되는 라인 위 또는 아래에 추가하고 싶으면 클릭 [Sed 텍스트 위 아래 추가](https://marindie.github.io/linux/Sed-Append-KR/)
