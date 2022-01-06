---
toc: true
title: "[Ubuntu] Mariadb 설치 방법 및 관련 이슈 정리"
description: "Ubuntu 에서 Mariadb 설치 방법 및 10.4 부터 변경된 내용에 대한 이슈랑 기타 내용 기록"
categories: [Linux]
tags: [Mariadb]
redirect_from:
  - /2022/01/06/
---

> Ubuntu 에서 Mariadb 설치 방법 및 10.4 부터 변경된 내용에 대한 이슈랑 기타 내용 기록

### Mariadb 설치 관련 명령어 {#toc1}

```md
sudo adduser mariadb sudo
sudo apt-get install mariadb-server

- 설치되는 버전을 잘보고 10.1 이후이면 아래의 방식으로 한다
sudo mysql 

- 만약에 에러가 난다면 아래의 명령어를 실행한다
service mysql start

권한관련해서 어떤 사용자를 사용할거라고 묻는데 mariadb 사용자를 숫자 번호로 선택한다

sudo mysql 로 로그인 이유가 궁금하면 https://www.nemonein.xyz/2019/07/2254/ 참고

- character set 확인
show variables like 'char%';

- 시간 확인
select @@global.time.zone, @@session.time_zone;

- 클라우드로 환경구축시 아래 명령어 실행
sudo timedatectl set-timezone 'Asia/Seoul'
date 로 확인
sudo systemctl restart mysql

select now(); 로 확인

- 외부에서 접속이 안될때
cat /etc/mysql/my.cnf

sudo vi /etc/mysql/mariadb.conf.d/50-server.cnf 에서 bind-address 를 0.0.0.0
포트도 필요하면 #port 주석 제거하고 변경

- database 에 접근할 권한이 없을 때
show databases;
use database_name;

권한 조회
select host, user from user;

권한 부여
grant all privileges on *.* to 'gitea'@'%' identified by 'gitea';
```

### Mariadb mysql 권한 및 정보 확인 관련 쿼리 또는 명령어 정리 {#toc2}

```md
- database 목록
show databases;

- database 선택
use database
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
