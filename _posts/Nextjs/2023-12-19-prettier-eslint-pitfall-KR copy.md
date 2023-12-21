---
toc: true
title: "Prettier 와 ESLint 의 단점으로 인한 고민"
description: "이번 글에서는 Prettier 와 ESLint 사용시 발생한 단점에 대해 다루어 보려 합니다. 장점이 많아 사용을 포기 할 수 없지만, 단점에 대해 알고 합의점을 찾은 부분에 대한 내용을 정리하였습니다."
categories: [nextjs]
tags: [nextjs]
redirect_from:
  - /2023/12/20/
---

### Prettier 와 ESLint 의 단점으로 인한 고민 {#toc1}

#### Prettier 와 ESlint 의 충돌로 인한 고민

- Prettier를 default formatter 로 설정 할 때의 단점과
- ESLint를 default formatter 로 설정 할 때의 단점이 저에게는 고민스러운 부분이였습니다.
- 먼저 단점을 보시죠.

```js
// 
import Image from "next/image";
import { type ReactNode } from "react";

export default function Home(): ReactNode {

```

- 해당 내용을 이해하기 위해서는 settings.json 에 해단 최소한의 이해는 있어야 합니다.
- 혹시나 settings.json 에 대한 이해가 없으신 분을 위해 제가 작성한 포스팅 링크를 공유합니다.
- :arrow_right: [nextjs 개발시 vscode 설정 관련 정보 모음](https://marindie.github.io/nextjs/2023-12-14-Nextjs-Tutorial-03-KR)