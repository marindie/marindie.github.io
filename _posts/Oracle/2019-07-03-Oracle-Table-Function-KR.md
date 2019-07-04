---
toc: true
title: "Oracle: Oracle Table Function 사용법 "
description: "Oracle Oracle Table Function 사용법 "
categories: [Oracle]
tags: [Oracle]
redirect_from:
  - /2019/07/03/
---

> Oracle Table Function 사용법

### 기록을 위한 설명 {#toc1}

```md
Oracle Doc 에는 아주 매력적인 기능을 설명해 놓아서 꼭 써보려고 기록한다.
Table Function 을 사용하면 다름과 같은 기능이 가능하다.
Multithreaded, concurrent execution of table functions
Parallel (한가지 최종목적을을 여럿 녀석이 달려들어 시간을 단축시는 방식)
Pipelining (Iterable 하게 전체 건을 다 모으지 않고 Collection 단위의 건들이 모아지면 바로 전달해주는 방식)
Streaming (멈춤없이 다름 단계로 계속 이어질 수 있음을 의미)

고로 멀티 쓰레드의 프로세스들이 대기 없이 각 Collection Data 들을 parallel 하게 select 를 해서 뭔가를 할 수 있게 도와주는것 같아서 관심을 가져봤다.

input 을 받는다고 한다.. collection type 또는 REF CURSOR. 
select 하고자하는 대상의 collection type 을 정의해 넣거나, 쿼리를 작성해서 넣으면 그걸 파싱해서 처리하는듯???
A table function can take a collection of rows as input. An input collection parameter can be either a collection type or a REF CURSOR

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io