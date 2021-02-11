---
toc: true
title: "Spring CompletableFuture 초간단 예제"
description: "Spring CompletableFuture 초간단 예제"
categories: [Spring]
tags: [Spring]
redirect_from:
  - /2019/06/25/
---

> Spring CompletableFuture 초간단 예제

### 기본 CompletableFuture + Custom Executor {#toc1}

```java
//Custom Executor Sample
ExecutorService executor = Executors.newFixedThreadPool(10);
CompletableFuture<Iterable<TestTableJpo>> future = CompletableFuture.supplyAsync(() -> testTableRepository.saveAll(src), executor);

//With Stream
class MyTask {
  private final int duration;
  public MyTask(int duration) {
    this.duration = duration;
  }
  public int calculate() {
    System.out.println(Thread.currentThread().getName());
    try {
      Thread.sleep(duration * 1000);
    } catch (final InterruptedException e) {
      throw new RuntimeException(e);
    }
    return duration;
  }
}

List<MyTask> tasks = IntStream.range(0, 10)
                                    .mapToObj(i -> new MyTask(1))
                                    .collect(toList());

List<CompletableFuture<Integer>> futures =
      tasks.stream()
           .map(t -> CompletableFuture.supplyAsync(() -> t.calculate(), executor))
           .collect(Collectors.toList());
```

```md
아래의 포스팅 참고 하였음
http://fahdshariff.blogspot.com/2016/06/java-8-completablefuture-vs-parallel.html

위의 포스트에서 parallel vs CompletableFuture 성능차이를 비교하는데,
결론은 parallel < CompletableFuture < CompletableFuture + Custom Executor 

추가적으로 구현하고자 하는 업무 특성에 따라, 결과를 기다린 후에 그 값을 바탕으로
다시 로직을 이어가는 경우와
전체 Thread Task 들이 완료된 후에 특정 업무를 구현하기 위한 경우 등등의 상황이 있을 수 있다.
```

### CompletableFuture + thenApply {#toc2}

이전에(혹은 어떤 특정 Task의) 완료된 결과 값을 바탕으로 다음 Task 를 이어서 진행한다.
주의점은 return type 이 thenApply 의 결과의 Data Type 이어야 한다는 점.

```java
CompletableFuture<String> completableFuture
  = CompletableFuture.supplyAsync(() -> "Hello");
 
CompletableFuture<String> future = completableFuture
  .thenApply(s -> s + " World");
```

### CompletableFuture + thenCompose {#toc3}

thenApply 가 이해가 간다면, 그와 동일함, 단지 파라미터로 이어서 진행 하고자 하는
CompletableFuture 을 파라미터로 받음

```java
CompletableFuture<String> completableFuture
  = CompletableFuture.supplyAsync(() -> "Hello");
 
CompletableFuture<String> future = completableFuture
  .thenApply(s -> s + " World");
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
