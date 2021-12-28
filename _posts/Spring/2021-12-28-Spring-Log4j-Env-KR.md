---
toc: true
title: "Spring Profiles Active Log4j 설정파일 위치 명시적으로 설정"
description: "Spring Profiles Active Log4j 설정파일 위치 명시적으로 설정"
categories: [Spring]
tags: [Log4j]
redirect_from:
  - /2021/12/28/
---

> Spring Profiles Active Log4j 설정파일 위치 명시적으로 설정

### web.xml 수정 {#toc1}

```xml
<context-param>
    <param-value>log4jConfigLocation</param-value>
    <param-value>classpath:log4j-${spring.profiles.active}.xml</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
</listener>
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
