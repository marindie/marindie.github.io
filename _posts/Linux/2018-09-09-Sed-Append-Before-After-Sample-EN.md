---
toc: true
title: "Sed Append Text Before After Sample"
description: "In this page, I will show you some examples about Linux sed command which append some text (before/after) matching text"
categories: [Linux]
tags: [Sed]
redirect_from:
  - /2018/09/09/
---

> In this page, I will show you some examples about Linux sed command which append some text (before/after) matching text.

# Assumption & Background

1. Your OS is CentOS 6.5

# Append Text Before Matching Text

```bash

# The following sed command will paste "Some More Text is appended" after matching text "Please Put it here"
sed -i'' -r -e "/Please Put it here/i\Some More Text is appended/" your_file.txt 

```

# Append Text After Matching Text

```bash

# The following sed command will paste "Some More Text is appended" after matching text "Please Put it here"
sed -i'' -r -e "/Please Put it here/a\Some More Text is appended/" your_file.txt 

```

# Those who are interested in basic sed, click this [link](https://marindie.github.io/blog/2018/09/09/Sed-Replace-Text-Sample-EN) 

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io