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

#### Prettier - Code formatter

- [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) 는 HTML, TypeScript, CSS 등과 같은 언어를 사용하여 개발시
- 코딩한 내용의 구조을 매우 보기 좋게 정렬 해주는 extension 입니다.

[![Prettier - Code formatter](/assets/images/screen/prettier_code_formatter.png)](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

#### Prettier ESLint

- [Prettier ESLint](https://marketplace.visualstudio.com/items?itemName=hb432.prettier-eslint-typescript) 는 TypeScript를 먼저 prettier 로 코딩 구조를 재정렬 하고
- 이후 ESLint 로 한번 더 코딩 구조를 재정렬 해주는 역할을 하는 extension 입니다.

[![Prettier ESLint](/assets/images/screen/prettier_eslint.png)](https://marketplace.visualstudio.com/items?itemName=hb432.prettier-eslint-typescript)

- 아시는 분은 아시는 setting.json (setting.json 에 대한 설명은 이후 아래에 더 부연설명 하였습니다.)에
- prettier 와 eslint 가 개발자 작성한 코딩 구조를 어떠한 형태로 재정렬 해주면 하는지
- 커스터마이징이 가능한 옵션에 대한 내용을 아래에 일부 가져와 적어두었습니다.
- prettier-eslint-typescript. 이후 eslintConfig 는 eslint 설정에 대한 부분을 나타내며
- prettier-eslint-typescript. 이후 prettierOptions 는 prettier 설정에 대한 부분을 나타냅니다.
- eslint 설정에 대하여 더 자세한 정보를 얻고 싶으시다면 제가 작성한 아래의 글을 참고하시기 바랍니다.
- :arrow_right: [vscode eslint 사용법, configure(설정) 방법 안내](https://marindie.github.io/nextjs/2023-12-19-eslint-Tutorial-01-KR)
- :arrow_right: [vscode prettier 사용법, configure(설정) 방법 안내](https://marindie.github.io/nextjs/2023-12-19-prettier-Tutorial-01-KR)

```json
{
   "typescript.tsdk": "node_modules/typescript/lib",
   "prettier-eslint-typescript.eslintConfig": {
      "rules": {
         "curly": "error",
         "dot-notation": "error",
         "no-undef-init": "error",
         "no-useless-rename": "error",
         "no-useless-return": "error",
         "no-var": "error",
         "object-shorthand": "error",
         "one-var": [ "error", { "initialized": "never", "uninitialized": "always" } ],
         "prefer-const": "error",
         "prefer-template": "error",
         "array-bracket-spacing": [ "error", "always" ],
         "generator-star-spacing": [ "error", "both" ],
         "space-before-function-paren": [ "error", "always" ],
         "yield-star-spacing": [ "error", "both" ]
      }
   },
   "prettier-eslint-typescript.prettierOptions": {
      "arrowParens": "avoid",
      "bracketSpacing": true,
      "printWidth": 120,
      "quoteProps": "as-needed",
      "singleQuote": true,
      "tabWidth": 3,
      "trailingComma": "none",
      "useTabs": false
   }
}
```

#### ESLint
#### ES7+ React/Redux/React-Native snippets
#### indent-rainbow
#### Auto Close Tag
#### Auto Rename Tag
#### colorize
#### GitLens
#### Git Graph
#### Git History

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
