---
toc: true
title: "Nature: 건강한 토양, 살아있는 생명 활동. Living Soil, Healthy Soil"
description: "토양에 대해 공부하고자 메모, 정리, 생각 보관 페이지" 
categories: [Nature]
tags: [Soil]
redirect_from:
  - /2021/01/28/
---

> 토양에 대해 공부하고자 메모, 정리, 생각 보관 페이지

### 피복작물의 중요성 {#toc1}

```md
 * 피복작물이란 - 토양 침식을 방지하거나 토양을 개선하기 위하여 토양의 전면을 덮어 재배하는 목초류.
 * 5월 ~ 9월 까지 옥수수를 대량생산하고, 그 이후에 10월 부터 4월 까지 피복 식물을 심어서
 * 토양의 탄소(Carbon) 을 흡수하여 토양의 생명 순환에 도움을 주는 활동을 이어간다
```


### 해결방법들 {#toc2}

Front 단에 설정을 하든 Back 단에 설정을 하든 둘중 하나만 하면 되는것으로 알고 있습니다.
하지만 크롬 브라우저 사용자라면, cors 플러그인을 설치하고 활성화를 매번 시켜주어야 정상 동작이 되는 것을 확인하였습니다.
React 를 기동 후 크롬을 열었을대 cors 플러그인이 활성화 되어 있어도, 정상 동작하지 않는 경우가 있었습니다.
이때 저는 다시 cors 를 비활성화 후에 활성화 하고 테스트해보았더니 통과가 되어 정상 적으로 데이터를 받았습니다.
참고 하시면 좋을것 같습니다.

```md

1. package.json 에 아래 내용 추가
proxy: http://localhost:8080

2. 크롬 브라우저에 cors plug-in 설치 후 enable

3. 2번까지만 하고 사용해도 무방하나, Back End 단에도 설정이 가능하다.
@RestController 어노테이션이 있는 java 파일에 아래 내용 추가
@Crossorigin(origin="*")

```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io