---
toc: true
title: " Mac 명령어 모음"
description: "Mac 명령어 모음"
categories: [Mac]
tags: [Mac]
redirect_from:
  - /2021/02/01/
---

> Mac을 사용하면서 필요했던 정보들을 기록

### 명령어 {#toc1}

```md
화면 최소화 : command + m
크롬 탭 전환 : option + command + 오른쪽 왼쪽
크롬 새탭 : command + t
크롬 새창 : command + n
프로그램들 전환 : 마우스 세손가락으로 오른쪽 왼쪽, control + 오른쪽 왼쪽
Zsh => Bash : chsh -s /bin/bash
기본 vimrc 위치 : /usr/share/vim/vimrc
vim 창 분할 : control + w 
vim 분할 상태에서 왼쪽 창으로 커서 이동 : control + w and l
```

### vim plugin 사용법

```md
#Install vim-plug : 
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
  https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim

vi .vimrc

# call plug#begin('~/.vim/plugged') ~  call plug#end() 사이에 plugin 관련 설정 기입
call plug#begin('~/.vim/plugged')
....
call plug#end()

#Plugin Install
:PlugInstall 
```

### vim colorscheme 변경

```bash
mkdir -p ~/.vim/colors
#해당 폴더에 XXX.vim colorscheme 파일 복사
#vimrc 안에 아래의 내용추가
colorscheme darkblue
syntax on
```

### ssh keygen and copy public key to remote server

```bash
# public/private key pair 생성
ssh-keygen -t rsa
# Enter key phase  보안이 신경 쓰인다면 암호를 추가 적으로 입력
# Enter key phase again

# Remote Server에 public 키 복사
cat ~/.ssh/id_rsa.pub | ssh -p portnum username@ipOrDomain "cat >> ~/.ssh/authorized_keys"
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
