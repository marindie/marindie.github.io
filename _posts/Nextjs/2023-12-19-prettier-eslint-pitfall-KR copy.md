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
- 이제 settings.json 에 제가 어떠한 형태로 Prettier 와 ESLint 를 사용하는지 설명 하겠습니다.

```json
// Ctrl + Shift + P 를 눌러 -> Open User Settings (JSON) 선택
// vscode 가 settings.json 파일을 없으면 만들어 줍니다.
// settings.json 파일의 설정에 관련된 내용중 이번 글과 관련된 설정 내용을 아래에 적었습니다.
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.organizeImports": "explicit",
    "source.addMissingImports": "explicit",
    "source.fixAll": "explicit"
  },
```

- 이제 Prettier 의 단점을 보시죠

```js
// 눈여겨 봐야 할 부분.
// 1. 큰따옴표로 String을 감싸고 있음
// 2. function Home 뒤에 이어서 공백없이 () syntax가 붙어져있는 상태
import Image from "next/image";
import { type ReactNode } from "react";

export default function Home(): ReactNode {
...
```

- 파일 저장을 시도 하면 변경된 사항이 없습니다.
- 그럼 제가 기대하는 수정 사항은 아래의 결과와 같습니다.

```js
// 변경이 되길 바라는 부분
// 1. 큰따옴표 String -> 작은따옴표 String 감싸기
// 2. function Home 뒤에 이어서 공백을 추가하고 () syntax가 붙어져있는 상태
import Image from 'next/image';
import { type ReactNode } from 'react';

export default function Home (): ReactNode {
...
```

```js
npm run lint -- --fix
```

- editor.formatOnSave: true 는 Ctrl + S 로 파일을 저장하면 해당 파일의 소스 코드를 format 하는것을 허용하는 옵션입니다.
- editor.defaultFormatter: esbenp.prettier-vscode 는 prettier 를 사용하여 format 을 진행하겠다는 의미입니다.
- editor.codeActionsOnSave: source.fixAll: explicit 은 모든 수정가능한 사항을 수정하겠다는 의미입니다.

- 해당 내용을 이해하기 위해서는 settings.json 에 해단 최소한의 이해는 있어야 합니다.
- 혹시나 settings.json 에 대한 이해가 없으신 분을 위해 제가 작성한 포스팅 링크를 공유합니다.
- :arrow_right: [nextjs 개발시 vscode 설정 관련 정보 모음](https://marindie.github.io/nextjs/2023-12-14-Nextjs-Tutorial-03-KR)