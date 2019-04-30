---
toc: true
title: "Docker: CentOS7 설치 관련 설정 세팅 기록"
description: "CentOS7에 Docker-CE 설치시 추가로 만진 부분들 정리"
categories: [Docker]
tags: [Docker]
redirect_from:
  - /2019/04/29/
---

> CentOS7에 Docker-CE 설치시 추가로 만진 부분들 정리.

### CentOS 7 Docker 를 사용한 Concourse 설치 {#toc1}

```bash

# yum 을통한 Docker Repository 추가시 CA 인증과 같은 문제가 발생해서 
/etc/yum.conf 파일 안에
sslverify=false 옵션 추가해서 사용했음.

# curl 사용시에도 SSL 사용을 건너띄고 하기 위해
curl -k (--insecure) 옵션을 주고 Docker Guide Document 에 있는 내용으로 Docker 를 설치했다.

# wget 사용시에도 CA 건너띄기 설정
wget --no-check-certificate https://...
alias wgetncc='wget --no-check-certificate'

# 아래는 Docker 에서 날린 설치 관련 명령어들.

sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io

# Docker 삭제 명령어
sudo yum remove docker-ce
sudo rm -rf /var/lib/docker

# Docker Compose 설치 명령어
sudo curl -k -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# 설치후 Binary 에 실행 권한 부여
sudo chmod +x /usr/local/bin/docker-compose
# 버전 확인
docker-compose --version
docker --version

# /etc/hosts 에 추가
export DOCKER_HOST=127.0.0.1

# Docker Start
sudo systemctl start docker

# Deploy Concourse using Docker Compose
wget --no-check-certificate https://raw.githubusercontent.com/starkandwayne/concourse-tutorial/master/docker-compose.yml
docker-compose up -d

# Docker Service Configuration File
/usr/lib/systemd/system/docker.service
# 포트 설정
ExecStart=/usr/bin/docker daemon -H fd:// -H tcp://0.0.0.0:5000
systemctl daemon-reload
sudo service docker restart

# Docker Daemon.json File
/etc/docker/daemon.json
{
  "insecure-registries" : ["myregistrydomain.com:5000"]
}


# Docker Proxy File
/etc/systemd/system/docker.service.d

# Reload Daemon Configuration 
sudo systemctl daemon-reload

# Check Daemon Configuration 
sudo systemctl show --property Environment docker

# Restart the Docker Daemon
systemctl restart docker.service

# Check Docker Status 
systemctl status docker.service

# Docker 포트 설정
sudo vi /lib/systemd/system/docker.service

```

### Concourse fly 명령어 {#toc2}

```bash

# Target Concourse
fly --target tutorial login --concourse-url http://127.0.0.1:8080 -u admin -p admin --ca-cert ~/POSCO.cer
fly --target tutorial login --concourse-url http://127.0.0.1:8080 -u admin -p admin --insecure
fly --target tutorial sync

# Check Configuration
cat ~/.flyrc

# Tutorial
git clone https://github.com/starkandwayne/concourse-tutorial.git
cd concourse-tutorial/tutorials/basic/task-hello-world

# Run Task
fly -t tutorial execute -c task_hello_world.yml

# Create Pipeline
cd ../basic-pipeline
fly -t tutorial set-pipeline -c pipeline.yml -p hello-world

```

### CA 설정 {#toc3}

```bash
# 인증서 파일 ex) ca.crt or ca.cer 파일을 아래의 경로에 두고 update 
cp ca.cer /etc/pki/ca-trust/source/anchors/ 
update-ca-trust 

# 아래의 내용은 삽질한 내용. 기록 차원에서 남김. CA 인증서를 추가하기만 하면 되는데
# SSL 인증을 By pass 하려고 시도해서 삽질하게 됨.
# docker-compose up -d 명령어 실행시 CA 인증 문제가 발생해서 아래처럼 실행
# 결론은 잘 안되서 포기했음. 그냥 매뉴얼 설치로 전환
################### START #########################
# Create a directory.
mkdir -p  /etc/pki/ca-trust/source/anchors
cd /etc/pki/ca-trust/source/anchors

# Download the DTR/Registry CA certificate.
curl -k https://registry-1.docker.io/ca -o /etc/pki/ca-trust/source/anchors/registry-1.docker.io.crt
chmod +x /etc/pki/ca-trust/source/anchors/registry-1.docker.io.crt

# Refresh the list of certificates to trust.
sudo update-ca-trust

openssl genrsa -out client.key 4096
openssl req -new -x509 -text -key client.key -out client.cert
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt
################### END #########################

```

###  Concourse 와 Postgres Manual 설치 {#toc4}

```bash

firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --reload

# Postgres
yum install postgresql-server postgresql-contrib
sudo postgresql-setup initdb
vi /var/lib/pgsql/data/pg_hba.conf ( ident -> md5 두곳 host 뭐시기..)
sudo systemctl start postgresql
sudo systemctl enable postgresql
adduser concourse
passwd concourse
sudo -i -u postgres
createuser concourse
createdb concourse
su - concourse
psql -d concourse
ALTER ROLE concourse WITH PASSWORD 'changeme';

# Concourse
sudo wget --no-check-certificate https://github.com/concourse/concourse/releases/download/v3.4.1/concourse_linux_amd64 -O /usr/bin/concourse
sudo wget --no-check-certificate https://github.com/concourse/concourse/releases/download/v3.4.1/fly_linux_amd64 -O /usr/bin/fly
sudo chmod +x /usr/bin/concourse /usr/bin/fly

sudo mkdir /opt/concourse

sudo ssh-keygen -t rsa -q -N '' -f /opt/concourse/session_signing_key
sudo ssh-keygen -t rsa -q -N '' -f /opt/concourse/tsa_host_key
sudo ssh-keygen -t rsa -q -N '' -f /opt/concourse/worker_key
sudo cp /opt/concourse/worker_key.pub /opt/concourse/authorized_worker_keys

sudo concourse web \
  --basic-auth-username admin \
  --basic-auth-password wony12 \
  --session-signing-key /opt/concourse/session_signing_key \
  --tsa-host-key /opt/concourse/tsa_host_key \
  --tsa-authorized-keys /opt/concourse/authorized_worker_keys \
  --postgres-user=concourse \
  --postgres-password=wony12 \
  --postgres-database=concourse \
  --external-url http://127.0.0.1:9000

# --no-really-i-dont-want-any-auth instead of basic-auth options

netstat -tnlp

sudo concourse worker \
  --work-dir /opt/concourse/worker \
  --tsa-host 127.0.0.1 \
  --tsa-public-key /opt/concourse/tsa_host_key.pub \
  --tsa-worker-private-key /opt/concourse/worker_key
```



[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io