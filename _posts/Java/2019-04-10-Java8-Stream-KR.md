---
toc: true
title: "JAVA8: JAVA8 부터 사용 가능한 Stream 관련 함수 사용 Sample 정리"
description: "JAVA8 부터 사용 가능한 Stream 관련 함수 사용법을 기록해 놓은 포스트 입니다."
categories: [JAVA8]
tags: [Stream]
redirect_from:
  - /2019/04/10/
---

> JAVA8 부터 사용 가능한 Stream 관련 함수 사용법을 기록해 놓은 포스트 입니다. 자꾸 잊어버려서 정리해놓습니다.

### List Object 의 각 Object를 새로운 Object로 mapping 후 List 로 리턴 {#toc1}

```java

this.playerRepository.findAll()
				.stream()
				.collect(Collectors.mapping(p -> new Player(p.getId(),p.getName(),p.getNum(),p.getPosition()), Collectors.toList()));

```

### List Object Filter 후 리턴 {#toc2}

```java

// 명령어 한줄로 사용시
foundBoard.getArticles().stream().filter(article -> article.getArticleId().equals(articleId)).findFirst().get();

// 명령어 한줄 이상일 때
foundBoard.getArticles().stream().filter(article -> {
			if(article.getArticleId().equals(articleId)){
				return true;
			}
			return false;
		}).findFirst().get();

```

### Map 형태의 key,value Stream 사용법 {#toc3}

```java

boardMap.values().stream().findFirst().get()

```

### JSON to List Object  {#toc4}

```java

		Type listType = new TypeToken<List<Item>>(){}.getType();
		List<Item> items = (new Gson()).fromJson(this.ItemsJson, listType);


// 반대로 List<Object> to JSON
    this.ItemsJson = (new Gson()).toJson(getItems());
```


[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io