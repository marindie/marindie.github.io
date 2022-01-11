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

wget https://dl.gitea.io/gitea/1.11.3/gitea

mv gitea-1.11.3-linux-amd64 gitea
chmod ugo+x gitea
sudo mv gitea /usr/local/bin
sudo mkidr -p /var/lib/gitea/{custom,data,indexers,public,log}
sudo chown git: /var/lib/gitea/{data,indexers,log}
sudo chmod 75
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
