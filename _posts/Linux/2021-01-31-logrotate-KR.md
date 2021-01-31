---
toc: true
title: "Linux: logrotate 사용법"
description: "logrotate 사용법, 옵션들 의미 및 장단점 소개"
categories: [Linux]
tags: [logrotate]
redirect_from:
  - /2021/01/31/
---

> logrotate 사용법, 옵션들 의미 및 장단점 소개

### logrorate 동작 방식 {toc#1}
```bash
# 기본적으로 logrotate 를 설치하면 cron.daily 에 자동으로 등록됨
cat /etc/cron.daily/logrotate
# 실제로 cron.daily 에서 사용되는 명령어
/usr/sbin/logrotate /etc/logrotate.conf
# 강제로 logrotate 를 실행하기위한 명령어
/usr/sbin/logrotate -f /etc/logrotate.conf
# 디버깅 모드로 테스트 해보기 위한 명령어
/usr/sbin/logrotate -d /etc/logrotate.conf
# 마지막 실행 로그 보기 
cat /var/lib/logrotate/status
```
```md
* 파일을 타겟으로 잡는 방식에 정규식을 주로 먹일 텐데, 거기에 걸려든 모든 파일에 대하여
  압축을 해주지는 않음. 
* 처리방식은 각 파일당 처리할 옵션을 정의 해서 처리함. 따라서 일별로 파일 압축후,
  월별로 해당 압축파일 다시 하나의 파일로 압축하는 것은 안됨. 별도로 처리해야함
* /etc/logrotate.conf 파일에 있는 설정이 global 설정
* 해당 정보를 기본적으로 읽지만, include /etc/logrotate.d 명령어로 인해
  /etc/logrotate.d 에 기술된 설정 파일들을 읽어서 처리하게 된다.
* 이때, 여기에 기록된 옵션들이 global 설정을 overwrite 하고, 
* 설정파일들 사이에 겹치는 설정 옵션들 중에서는 나중에 불러들여서 읽은 파일의 설정을 최종적으로 사용하게 된다.
* Each  configuration  file  can  set  global  options (local definitions override global ones, and later definitions override earlier ones)
```

### logrorate 장점 {toc#2}

```md
* 로그를 rotate 해서 적절한 사이즈 내에서 로그들이 순환하도록 하기에는 적합
```

### logrorate 단점 {toc#3}
```md
* 여러파일을 하나의 파일로 압축을 해주지 못함
* 일별로 로그를 압축하는것 까지도 사용하기에는 무방하지만, 일별, 월별, 연별로 
  파일들을 관리하기에는 부적합
```

### logrotate 옵션 {toc#4}
```bash
# https://man7.org/linux/man-pages/man5/logrotate.conf.5.html 사이트 참고를 추천

daily
# 일별 rotate, 실제로 동작하는 조건은 해당 시간이 00시00분 일때 도는것 같음. 
# -f 옵션을 사용하면 무조건 실행해서 rotate 를 하지만 daily, weekly, monthly, yearly 같은 경우는 각 시간이 실제로 월초, 연초, 다음날 여부를 확인해서 동작한다  
rotate 36500 
# 이 값은 daily(weekly,monthly,yearly) * rotate 옵션 숫자까지 rotate 한다는 뜻 
# 1 * 36500 는 100년치 숫자까지 계속 숫자가 증가하는 형태로 rotate 한다. 
# Ex filename.1 ~ filename.36500 까지 하고 나서 filename.1 다시 재사용
missingok
# 해당 파일이 존재 하지 않아도 에러 발생시키지 않음
notifempty
# 만약에, 파일의 사이즈가 0이면, rotate 대상으로 삼지 않음
compress
# 압축함. default 는 gzip 인것으로 알고 있음
compressext .zip
# 압축시 붙일 확장자 이름
dateext
# -%Y%M%d 를 기본으로 함 Ex. filename-20210101
create 0600 root root
# rotate 파일을 만들면서 파일 권한 그룹 사용자 지정
```

### logrotate 예제 {toc#4}
```bash
vi /etc/logrotate.d/tomcat

# 아래는 해당 파일의 내용
/contents/logs/ment/tomcat/catalina.out{
daily
rotate 36500
missingok
notifempty
dateext
dateyesterday 
# daily 가 다음날 00시00분에 도는데, 이때, 미래의 날짜로 파일이 생성되기에 이전 날짜로 파일 생성하도록 요청
copytruncate 
# catalina.out-20200101.zip 파일 생성하고, cat /dev/null > catalina.out 명령어 먹이는 것과 똑같이 동작. 이점은 그냥 롤링하면 더이상 catalina.out 에 로그가 안쌓임. 재기동해야 쌓이는데, copytruncate 하면, 이어서 계속 로그 쌓임
compress
compresscmd /usr/bin/zip
compressext .zip
olddir /contents/logs/ment/tomcat/daily
# rotate 한 파일을 보관할 폴더 지정
}
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io