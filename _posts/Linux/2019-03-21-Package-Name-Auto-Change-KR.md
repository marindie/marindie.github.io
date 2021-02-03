---
toc: true
title: " Java Package Name 자동 변환 프로그램 (Windows 에서 Cygwin 으로 사용가능)"
description: "Linux 명령어를 사용하여 Package 폴더 하위의 파일, 폴더, 패키지명을 자동변환 해주는 프로그램입니다."
categories: [Linux]
tags: [find]
redirect_from:
  - /2019/03/21/
---

> Linux 명령어를 사용하여 Package 폴더 하위의 파일, 폴더, 패키지명을 자동변환 해주는 프로그램입니다.

### 배경지식 {#toc1}

1. Windows Cygwin 에서 테스트 하였습니다.

### 소스 {#toc2}

```md
#/bin/bash
SRC=$1
TG=$2

NUM=$(find . -type d -name "${SRC}*" | wc -l)
echo $NUM
if [ ! $NUM -eq 0 ]; then
	for i in $(eval echo "{1..$NUM}")
	do
		find . -type d -name "${SRC}*" -print -quit | while read f; do mv $f $(echo $f | sed "s/${SRC}/${TG}/"); done
	done
fi
find . -type f -name "${SRC}*" | while read f; do mv $f $(echo $f | sed "s/${SRC}/${TG}/"); done
find . -type f -name "*" ! -name "template.sh" | while read f; do sed -i "s/io.test/com.test.main/g" $f; done
find . -type f -name "*" ! -name "template.sh" | while read f; do sed -i "s/${SRC}/${TG}/g" $f; done

```

### 내용설명 {#toc3}

```md
첫번째 파라미터의 값에 현재 공통으로 사용하는 패키지 이름을 넣고 (Ex: cart)
두번째 파라미터의 값에 변경하고자 하는 패키지 이름을 넣고 실행하는 프로그램입니다. (Ex: user)
이 프로그램 이름을 template.sh 라고 하면, "./template.sh cart user" 라고 실행하면 됩니다.
파라미터 null 체크는 편의상 스킵하였습니다. 에러 처리 추가하셔도 좋겠지요.

패키지 구조 예시

cart
    - cart-domain
        - src
            - main
                - java
                    - com
                        - cart
                            - domain
                            - cart.java
    - cart-service
        - src
            - main
                - java
                    - com
                        - cart
                            - service
                            - cartService.java                            
pom.xml

1. 먼저 폴더명을 바꾸는데요. 이때 발생하는 에러가 있습니다.
   먼저 cart-domain 이 user-domain 으로 mv 되면, 
   그 하위 폴더를 변경하고자 할때 find 결과에서는 기존 변경 전 경로를 가지고 있어서
   에러가 발생하게 됩니다.
   그걸 해결하고자 저는 루프를 돌려서 find 결과 첫번째 녀석을 바꾸는 작업을 여러번 돌리도록 하였습니다.
   그 카운트를 위해 NUM 에 find 결과를 담았습니다.
2. 파일 이름 변경은 그다지 어렵지 않기에 find 하고 결과를 바로 변경하였습니다.
3. 만약 pom.xml 에 package 명이 들어가는데, 
   그 이름이 여러곳에 있고 전체 변경이 필요하여, sed 를 사용하여 io.test 를 com.test.main 으로 변경하는 코드를 작성하였습니다.
4. 마지막으로 cart가 들어가는 단어를 찾아서 user 변경하는 작업을 진행하였습니다.

해당 package 구조대로 여러개의 추가 프로젝트를 많이 만들게 되는 경우가 있을때 사용하면 좋습니다.
저는 비슷한 구조의 package 를 앞으로도 계속 사용하게 될거 같아서 만들어 놓았습니다.

```

### 추가적으로 기본적인 sed 명령어 관련 POST 링크 {#toc4}

클릭 => [sed 사용법](https://marindie.github.io/linux/Sed-Replace-Text-Sample-EN/) 

감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io



