---
toc: true
title: "[Ubuntu] Gitea 설치 명령어 정리"
description: "Ubuntu 20.04 에서 Gitea 설치 관련 명령어 정리"
categories: [Linux]
tags: [Gitea]
redirect_from:
  - /2022/01/06/
---

> Ubuntu 20.04 에서 Gitea 설치 관련 명령어 정리

### Gitea 설치 관련 명령어 {#toc1}

```md
sudo apt-get install git -y

sudo adduser --system --group --disabled-password --shell /bin/bash --home /home/git --gecos 'Git Version Control' git
또는 sudo adduser git

wget https://dl.gitea.io/gitea/1.11.3/gitea-1.11.3-linux-amd64

mv gitea-1.11.3-linux-amd64 gitea
chmod u+x gitea
sudo mv gitea /usr/local/bin
sudo mkdir -p /var/lib/gitea/{custom,data,indexers,public,log}
sudo chown git: /var/lib/gitea/{data,indexers,log}
sudo chmod 750 /var/lib/gitea/{data,indexers,log}
sudo mkdir /etc/gitea
sudo chown root:git /etc/gitea
sudo chmod 770 /etc/gitea
sudo wget https://raw.githubusercontent.com/go-gitea/gitea/master/contrib/systemd/gitea.service -P /etc/systemd/system/
sudo vi /etc/systemd/system/gitea.service
파일 안에서
User=git => User=root 로 변경 후 저장

설정 파일 위치
/etc/gitea/app.ini
/usr/local/bin/custom/conf/app.ini

Reload gitea service
sudo systemctl daemon-reload

- 부팅시 서비스 시작
sudo systemctl enable --now gitea

- 서비스 비활성화
sudo systemctl disable gitea.service

- 서비스 중지
sudo systemctl stop gitea.service

- 서비스 목록
sudo systemctl list-units --type=service

gitea 실행시키면 기본 포트로 3000 으로 실행됨. 
음.. 파일에서 포트 변경으로 하는 방법은 잘 모르는 상황이고..

gitea web -p 9090 등으로 gitea 실행시 포트를 입력해서 해당 포트로 띄워서 사용중
gitea web --config /usr/local/bin/custom/conf/app.ini
```
