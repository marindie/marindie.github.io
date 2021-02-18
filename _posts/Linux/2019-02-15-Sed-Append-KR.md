---
toc: true
title: " Sed 명령어 특정 라인 위 또는 아래에 Text 추가 하기"
description: "Linux sed 명령어를 사용하여 특정 라인 위 또는 아래에 Text 추가하는 방법을 다뤄 보도록 하겠습니다."
categories: [Linux]
tags: [Sed]
redirect_from:
  - /2019/02/15/
---

> Linux sed 명령어를 사용하여 특정 라인 위 또는 아래에 Text 추가하는 방법을 다뤄 보도록 하겠습니다.

### 배경지식 {#toc1}

1. CentOS 6.5 에서 테스트 하였습니다.

### 매칭 되는 라인 위에 Text 추가 하기 {#toc2}

```md
# "Some More Text is appended" Text 가 "Please Put it here" 있는 라인 위에 추가 됩니다.
sed -i'' -r -e "/Please Put it here/i\Some More Text is appended/" your_file.txt 
```

### 매칭 되는 라인 아래에 Text 추가 하기 {#toc3}

```md
# "Some More Text is appended" Text 가 "Please Put it here" 있는 라인 아래에 추가 됩니다.
sed -i'' -r -e "/Please Put it here/a\Some More Text is appended/" your_file.txt 
```

### 추가적으로 기본적인 sed 명령어 관련 POST 링크 {#toc4}

클릭 => [sed 사용법](https://marindie.github.io/linux/Sed-Replace-Text-Sample-KR/)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
