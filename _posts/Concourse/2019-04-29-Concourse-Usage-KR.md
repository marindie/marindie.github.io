---
toc: true
title: " Concourse fly 사용법"
description: "CentOS 7에 설치된 Concourse 사용법 정리"
categories: [Concourse]
tags: [Concourse]
redirect_from:
  - /2019/04/29/
---

> CentOS 7에 설치된 Concourse 사용법 정리

### Concourse fly 명령어 {#toc1}

```md
# Docker 실행
service docker start

# https://docs.docker.com/compose/reference/up/
docker-compose up -d

# Target Concourse
fly --target tutorial login --concourse-url http://127.0.0.1:8080 -u admin -p admin --ca-cert ~/ca.cer
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
