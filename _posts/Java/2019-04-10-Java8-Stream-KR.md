---
toc: true
title: " Java8 부터 사용 가능한 Stream 관련 함수 사용 Sample 정리"
description: "Java8 부터 사용 가능한 Stream 관련 함수 사용법을 기록해 놓은 포스트 입니다."
categories: [Java]
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

// 기존의 List 로 Return
this.playerRepository.findAll()
				.stream()
				.collect(Collectors.toList()));
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

### Nested Loop 형태를 Stream 형태로 구현 {#toc4}

```java
// anyMatch 를 사용해서 또하나의 List 에 있는 정보를 비교 가능함.
// Stream 은 한번만 사용가능. For loop 안에 Stream 사용하면 에러남.

List<Car> filteredCars =
    cars.stream()
        .filter (
            car -> wheels.stream()
                         .anyMatch(wheel -> wheel.getColor() == car.getColor() &&      
                                            wheel.isWorking()))
        .collect(Collectors.toList());
```

### JSON to List Object  {#toc5}

```java

		Type listType = new TypeToken<List<Item>>(){}.getType();
		List<Item> items = (new Gson()).fromJson(this.ItemsJson, listType);


// 반대로 List<Object> to JSON
    this.ItemsJson = (new Gson()).toJson(getItems());

// Gson format 적용
Gson gson = new GsonBuilder().setPrettyPrinting().create();
gson.toJson(Obj);

```

### JSON from/to Object  {#toc6}

```java
ObjectMapper mapper = new ObjectMapper();
Test obj = new Test();

//Object to JSON in file
mapper.writeValue(new File("c:\\file.json"), obj);

//Object to JSON in String
String jsonInString = mapper.writeValueAsString(obj);
```

### Sort 사용법  {#toc7}

```java
attrs.get().stream().sorted().collect(Collectors.toList());

//관련 형태 사용법
.sorted(Comparator.reverseOrder())
.sorted(Comparator.comparing(Student::getAge)
.sorted(Comparator.comparing(Student::getAge).reversed())

// 사용하고자 하는 List Object Class 에 compareTo override 및 Comparable implements
public class AttrsJpo implements Comparable<AttrsJpo>{
	@Override
	public int compareTo(AttrsJpo o) {
		// TODO Auto-generated method stub
		// 원하는 멤버 변수 비교 세팅하면 됨.
		return id.compareTo(o.getId());
	}
}
```
