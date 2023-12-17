---
toc: true
title: "nextjs vscode 환경 세팅 관련정보 및 개발시 매우 도움이 되는 extension 모음"
description: "이번 글에서는 nextjs 개발시 사용하는 vscode IDE 환경 세팅 및 nextjs 개발시 매우 도움이되는 extension들에 대한 내용을 정리하였습니다."
categories: [nextjs]
tags: [nextjs]
redirect_from:
  - /2023/12/17/
---

### nextjs 개발시 매우 도움이 되는 extension 모음 {#toc1}

- Prettier - Code formatter
- 
- Prettier ESLint
- ESLint
- ES7+ React/Redux/React-Native snippets
- indent-rainbow
- Auto Close Tag
- Auto Rename Tag
- colorize
- GitLens
- Git Graph
- Git History

[Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

[![Prettier - Code formatter](/assets/images/screen/prettier_code_formatter.png)](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

[![text](https://microsoft.github.io/vscode-remote-release/images/ssh-readme.gif)](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
[![text](/assets/images/screen/aaaaaa.png)](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)



- vscode 환경 세팅은 .
- 포트 변경을 위해서는 package.json 파일안의 json 내용에서 scripts 의 dev 내용을 수정하면 됩니다.
- 기존 "dev": "next dev" 를 "dev": "next dev -p 5000" 으로 수정후
- npm run dev 명령어로 서버를 다시 시작하면 변경된 포트가 적용됩니다.

```json
{
  "name": "nextjs",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev -p 5000",
    "build": "next build",
    "start": "next start",
    "lint": "npx eslint",
  },
  "dependencies": {
    ...  
  },
  "devDependencies": {
    ...
  }
}
```

- 아래는 변경후 npm run dev 명령어로 서버 포트가 변경되는것을 명령 프롬프트에서 확인한 스샷

![서버실행성공](../../assets/images/screen/서버실행성공.png)

- 서버 포트 변경에 대하여 알아보았습니다.
- 다음은 nextjs 개발시 사용할 IDE 툴 Vscode의 개발 환경 세팅 및 nextjs 개발시 매우 도움이되는 extension 들에 대해 알아봅시다.
- :arrow_right: [nextjs vscode 개발 환경 세팅 및 개발시 매우 도움이되는 extension](https://marindie.github.io/nextjs/2023-12-14-Nextjs-Tutorial-03-KR)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
