---
toc: true
title: " Find Command Usage"
description: "Basic Find Command Usage"
categories: [Linux]
tags: [Find]
redirect_from:
  - /2018/10/19/
---

> In this page, I will show you some of basic find command in case someone forgot to use it.

### Find Command with Options

```bash 

# Some of Useful Options that I use often
# -t type, -name filename (Regex Possible)
# -maxdepth 1 (current path only)
# Ex)
find / -type f -name "*.java"
find /home -type d -name "*test"
find . -maxdepth 1 -name "*.txt"

```

### Run Shell command after mathing file found

```bash

# To execute linux command for each files that were found, type "{} \;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} \;

# To execute linux command for all files that were found, type "{} +;" in the end
# Ex)
find . -name "*.txt" -exec grep chrome {} +;

```

### Find file whose size is over 20Mb

```bash

find /usr -size +20000 -print -exec ls -alt {} +; 

```

### Find file where modified date is over 3 days in current folder level. Print Timestamp Format
```bash
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n"
find . -maxdepth 1 -type f -ctime +3 -printf "%p %TY-%Tm-%Td %TH:%TM:%TS %Tz\n" -delete
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io