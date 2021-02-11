---
toc: true
title: " Setfacl Getfacl Command"
description: "Basic XXXfacl Command"
categories: [Linux]
tags: [Setfacl, Getfacl]
redirect_from:
  - /2018/10/19/
---

> In this page, I will show you some of basic setfacl and getfacl command for someone interested in.

### Setfacl

```md

# 'u' stands for user, 'g' stands for group, 'o' stands for others
# -m means modify
setfacl -m u:user1:rwx /DATA/work/CP_transaction
setfacl -m g:devel:rw /project/confi

# default user, group setting.
setfacl -d -m g::rwx /<directory>

# Apply to Subfolders -R
setfacl -R -d -m u::rwx /file
setfacl -R -d -m g::rwx /file

# Delete 
setfacl -x u:user1 /etc/sysconfig/iptables
```

### Getfacl

```md
getfacl /DATA/work/CP_transaction
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
