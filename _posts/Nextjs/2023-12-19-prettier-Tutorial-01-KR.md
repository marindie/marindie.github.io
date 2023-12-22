---
toc: true
title: "vscode prettier 사용법, configure(설정) 방법 안내"
description: "이번 글에서는 vscode 에서 prettier 사용법에 대해 다루어 보려 합니다."
categories: [nextjs]
tags: [nextjs]
redirect_from:
  - /2023/12/19/
---

### Prettier 사용법에 대한 설명 전에 언급할 내용 {#toc1}

- ESLint 와 함게 Prettier를 주로 사용하고 저도 사용중인데요.
- vscode 에서 두 가지 모두 사용하고 있고, 파일을 저장 할 때,
- 기본 Fomatter 로 Prettier를 사용하도록 하여 에러를 자동 수정하도록 사용중입니다.

#### Default Formatter 로 Prettier 선택하기

- Ctrl + , for Window // Command + , for Mac 키를 눌러 settings 창을 열어봅시다.
- 검색 Input box 에서 default format 을 치면 아래의 스샷처럼 Editor: Default Formatter 를 선택할 수 있게 됩니다.
- prettier 를 선택하면 됩니다.
  
![Default Formatter](/assets/images/screen/default-formatter.png)

- 해당 설정 정보는 Ctrl + Shift + p for Window 키를 누르고
- Open User settings 를 검색하여 들어가시면 settings.json 파일에 "editor.defaultFormatter": "esbenp.prettier-vscode", 값이 있을 것입니다.

```json
{
  ...생략
  "editor.defaultFormatter": "esbenp.prettier-vscode"
  ...생략
}
```

- ESLint 설정에 대한 자세한 정보는 제가 작성한 아래의 글을 참고하시기 바랍니다.
- :arrow_right: [eslint 사용법, configure(설정) 방법 안내](/nextjs/eslint-Tutorial-01-KR)
- Prettier vscode Extension 설치를 원하시는 분은 아래의 이미지를 클릭하시면 됩니다.

[![Prettier - Code formatter](/assets/images/screen/prettier_code_formatter.png)](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

#### Prettier를 사용하는 이유

- Prettier 를 사용하는 이유는 HTML Format 을 도와주어 저의 시간을 많이 단축시켜주기 때문입니다.
- 파일 저장시 기본 Formatter로, ESLint 와 Prettier 둘다 동시에 사용 할 수는 없습니다.
- 둘 중 하나만 선택해야 합니다.
- ESLint 를 사용하면 Prettier 의 HTML Format 수정을 지원 못받게 되는데, 이로 인한 시간이 좀 더 들어가는 경향이 있습니다.
- 그렇다고 해서 Prettier 와 ESLint 규칙을 함께 사용 못 한다는 것은 아닙니다.
- 명령 프롬프트와 같은 실행 창에서 명령어를 통해 자동 수정을 돌릴 수 있기 때문입니다.

#### 명령 프롬프트 에서 Prettier 와 ESlint 자동 수정 명령어 실행

```js
// Prettier 문법 에러 수정 명령어
npx prettier src --check --write
// ESLint 문법 에러 수정 명령어
npx eslint --ext .js,.ts,.tsx --ignore-pattern "postcss.config.js" --ignore-pattern "*.ico" --ignore-pattern "*.css" --fix "src/**"
```

#### package.json 에 Prettier 와 ESLint 자동 수정 명령어 설정하기

- 위의 명령어를 매번 치는것은 다소 번거롭습니다.
- package.json 의 scripts 안에 설정한 내용은, 명령 프롬프트에서 npm run "scripts 안에 정의해둔 key값" 형태로 호출 가능합니다.
- Ex. npm run dev, npm run format, 등
- 명령 프롬프트에 직접 명령어를 칠대는 큰따옴표를 사용해서 구분지어 주어야 하지만,
- package.json 에서는 추가적인 큰 따옴표 없이 value 의 큰 따옴표 안에 모두 작성하면 알아서 동작합니다.

```json
//package.json
...생략
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "npx eslint",
    "lint:fix": "npm run lint src/** -- --fix --ext .js,.ts,.tsx",
    "prettier": "npx prettier src --check",
    "prettier:fix": "npm run prettier -- --write",
    "format": "npm run prettier:fix && npx eslint --ext .js,.ts,.tsx --ignore-pattern postcss.config.js --ignore-pattern *.ico --ignore-pattern *.css --fix src/**"
  },
...생략
```

- 위의 예시를 기준으로 npm run format 을 하였을때, prettier 부분이 어떻게 동작하는지 추가 설명드리겠습니다.
  1. npm run prettier:fix => scripts 에서 "prettier:fix" 키에 해당하는 value를 실행
  2. npm run prettier -- --write => scripts 에서 "prettier" 키에 해당하는 value를 실행
  3. npx prettier src --check 을 실행 
  4. -- --write 부분이 남는데, 이부분은 -- 뒤 부터는 --write 와 같은 옵션 명령어들을 글자 붙여주는 동작을 해주라는 의미.
  5. -- <= 이 표시를 해야 옵션 syntax 들이 정상적으로 들어가서 동작됩니다.

### Prettier 사용법 {#toc2}

- Prettier 사용법은 ESLint 에 비하면 매우 간단한 편입니다.
- 규칙이 매우 많은 편은 아닙니다.
- 먼저 Prettier 의 Playground 에 들어가서 몇가지 옵션을 활성/비활성화 하면서 눈에 익힙니다.
  - :arrow_right: [Prettier Playground 바로가기](https://prettier.io/playground)
- 해당 사이트의 화면은 매우 간단해서 글로 설명해도 충분합니다.
- 좌측 하단의 "Copy config JSON" 버튼을 클릭하면 Playground 에서 사용하고 있는 옵션값들을 json 형태로 복사해줍니다.
- 아래는 제가 사용하는 옵션값들 입니다.
- 해당 옵션 값을 프로젝트폴더의 .prettierrc 파일명으로 만든후 값을 복사하고 저장하면 동작합니다.

```json
// 프로젝트폴더/.prettierrc
{
  "arrowParens": "always",
  "bracketSameLine": false,
  "bracketSpacing": true,
  "semi": false,
  "singleQuote": true,
  "jsxSingleQuote": true,
  "quoteProps": "as-needed",
  "trailingComma": "es5",
  "singleAttributePerLine": true,
  "htmlWhitespaceSensitivity": "css",
  "vueIndentScriptAndStyle": false,
  "proseWrap": "always",
  "insertPragma": false,
  "printWidth": 100,
  "requirePragma": false,
  "tabWidth": 2,
  "useTabs": false,
  "embeddedLanguageFormatting": "off"
}
```

#### 파일 저장시 바로 Prettier Format 적용 방법

- 해당 설정 정보는 Ctrl + Shift + p for Window 키를 누르고
- Open User settings 를 검색하여 들어가시면 settings.json 파일에
- 아래와 같은 설정 값을 추가해 줍니다.
- 또는 Ctrl + , for Window 키를 눌러 settings 창을 연 다음,
- formatOnSave를 검색하여 설정 할 수도 있습니다.

```json
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.organizeImports": "explicit",
    "source.addMissingImports": "explicit",
    "source.fixAll": "explicit"
  },
```

### 파일 저장시 수정되는 일들 관련 특이사항  {#toc3}

- 파일을 저장해 보면 상황에 따라 여러가지 수정이 일어 납니다.
- 모든 상황을 언급 하긴 어렵고, 저의 상황 몇가지를 공유합니다.

#### Object 형태의 key value 에서 맨 마지막 key value 뒤에 콤마가 삭제되는 경우

- 정확한 원인은 모르지만, .prettierrc 파일에 trailingComma 를 all 로 설정하여도
- 파일 저장시 마지막 콤마는 여전히 삭제 됩니다.
- 저는 그냥 마지막 콤사는 없는 형태에 적응해서 쓰고 있습니다.

#### Object 형태의 key value 에서 key value 를 큰 따옴표(") 가 아닌 작은 따옴표(') 로 바뀌는 경우

- 저는 json 파일 형태에는 vscode json 언어 피처를 사용하는데요.
- 이거를 사용하지 않고 저장했더니 Object key value 를 작은 따옴표(')로 바꿔버려서
- 큰 따옴표(")를 유지하기 위해 추가 하였습니다.

```json
//settings.json Ctrl + Shift + p (Open User Settings)
  ...생략
  "[json]": {
    "editor.defaultFormatter": "vscode.json-language-features"
  },
  ...생략
```

- 다른 방식으로는 Prettier 설정 파일인 .prettierrc 에서
- "singleQuote": false, 로 설정하면 큰 따옴표(")로 유지 됩니다.
- 이때 .prettierrc 파일을 수정후 바로 저장하면 변경 사항이 동작하지 않습니다.
- 최소한 다른 파일을 탭으로라도 이동 하였다가 와야 변경사항이 동작하니 이점 유의하시기 바랍니다.
- 저는 성격이 급해서 "아 왜 바꿨는데 그대로지 하면서 승질냈습니다."

```md
끝까지 읽어 주셔서 감사합니다.
도움이 되었으면 좋겠네요.
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
