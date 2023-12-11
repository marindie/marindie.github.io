---
toc: true
title: "[Ubuntu] Mariadb 설치 방법 및 관련 이슈 정리"
description: "Ubuntu 20.04 에서 Mariadb 설치 방법 및 10.4 부터 변경된 내용에 대한 이슈랑 기타 내용 기록"
categories: [Linux]
tags: [Mariadb]
redirect_from:
  - /2022/01/06/
---

> Ubuntu 20.04 에서 Mariadb 설치 방법 및 10.4 부터 변경된 내용에 대한 이슈랑 기타 내용 기록

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-5746287019974907"
     crossorigin="anonymous"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout-key="-gw-3+1f-3d+2z"
     data-ad-client="ca-pub-5746287019974907"
     data-ad-slot="5182617875"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

### Mariadb 설치 관련 명령어 {#toc1}

```md
sudo adduser mariadb
sudo usermod -a -G sudo mariadb
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
select @@global.time_zone, @@session.time_zone;

- 클라우드로 환경구축시 아래 명령어 실행
sudo timedatectl set-timezone 'Asia/Seoul'
date 로 확인
sudo systemctl restart mysql
select now(); 로 확인

- 외부에서 접속이 안될때
sudo vi /etc/mysql/mariadb.conf.d/50-server.cnf 에서 bind-address 를 0.0.0.0

파일이 없으면 아래의 명령어로 폴더 위치 확인
cat /etc/mysql/my.cnf
포트도 필요하면 #port 주석 제거하고 변경

- mariadb user 생성
create user 'gitea'@'%' identified by 'gitea';

- database 선택
show databases;
create database gitea;
use database_name;

- 권한 부여
grant all privileges on *.* to 'gitea'@'%' identified by 'gitea';

- 권한 조회 (mysql database 혹은 database 접속 안한 상태에서 아래 명령어)
select host, user from user;
```

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-5746287019974907"
     crossorigin="anonymous"></script>
<ins class="adsbygoogle"
     style="display:block; text-align:center;"
     data-ad-layout="in-article"
     data-ad-format="fluid"
     data-ad-client="ca-pub-5746287019974907"
     data-ad-slot="2220653635"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
