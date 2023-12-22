---
toc: true
title: "eslint 사용법, configure(설정) 방법 안내"
description: "이번 글에서는 eslint 사용법에 대해 다루어 보려 합니다. vscode extension 과 npm(yarn) install 을 통한 패키지 설치에 대한 부분도 언급하여 다루고 있습니다."
categories: [nextjs]
tags: [nextjs]
redirect_from:
  - /2023/12/19/
---

### eslint 사용법 {#toc1}

- 혹시라도 ESLint Extension 을 설치 하지 못한 분은 아래의 링크를 통해 설치를 진행해 보시기 바랍니다.

#### ESLint Extension

- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 는 TypeScript를 먼저 prettier 로 코딩 구조를 재정렬 하고
- ESLint를 설치하면 Javascript 나 TypeScript 확장자 파일내에 개발자가 작성한 문법이 ECMA 기준에 부합한지 판단 하고 경고과 에러로 표시가 뜹니다.
- 여러가지 Rule 이라고 하는 규직을 기본값이 정해진 상태로 사용하게 되며
- 사용자가 설정 내용을 잘 아는 만큼 비활성화 활성화 선택을 통해 어느정도 커스터마이징 가능합니다.
- 이후 ESLint 로 한번 더 코딩 구조를 재정렬 해주는 역할을 하는 extension 입니다.

[![ESLint](/assets/images/screen/eslint.png)](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

#### eslint 설치

- eslint 를 사용하기 위해 npm 또는 yarn 명령어를 통해 eslint 와 관련된 패키지를 인스톨 합니다.

```js
// 맨뒤에 --dev 를 붙여 devDependencies에 추가할 수도 있습니다. 선택사항
// npm 대신 yarn으로 사용해도 똑같습니다.
npm install eslint
npm install eslint-plugin-prettier
npm install eslint-plugin-react
npm install eslint-plugin-react-hooks
// jsx syntax 관련 eslint
npm install eslint-plugin-jsx-a11y
```

#### eslint 설치시작. 설정에 관련된 질문들

```js
// 이후 명령 프롬프트에 다음과 같은 명령어를 입력합니다.
npm init @eslint/config
// 또는
yarn init @eslint/config
```

- 이후 각 질문에 대한 선택을 통해 기본 설정을 잡아가게됩니다.
- 아래의 스샷은 제가 선택한 설정 입니다.

![eslint-config-01](/assets/images/screen/image-01.png)

![eslint-config-02](/assets/images/screen/image-02.png)

![eslint-config-03](/assets/images/screen/image-03.png)

![eslint-config-04](/assets/images/screen/image-04.png)

![eslint-config-05](/assets/images/screen/image-05.png)

![eslint-config-06](/assets/images/screen/image-06.png)

![eslint-config-07](/assets/images/screen/image-07.png)

![eslint-config-08](/assets/images/screen/image-08.png)

![eslint-config-09](/assets/images/screen/image-09.png)

![eslint-config-10](/assets/images/screen/image-10.png)

- 해당 설정을 진행하시고 나면 package.json 에 추가적인 패키지들이 설치되어 있음을 알 수 있습니다.

```json
// package.json 파일
{
  "name": "nextjs-tutorial",
  "version": "0.1.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "eslint-plugin-jsx-a11y": "^6.8.0",
    "eslint-plugin-prettier": "^5.1.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "next": "14.0.4",
    "react": "^18",
    "react-dom": "^18"
  },
  "devDependencies": {
    "@types/node": "^20",
    "@types/react": "^18",
    "@types/react-dom": "^18",
    "@typescript-eslint/eslint-plugin": "^6.15.0",
    "autoprefixer": "^10.0.1",
    "eslint": "^8.56.0",
    "eslint-config-next": "14.0.4",
    "eslint-config-standard-with-typescript": "^43.0.0",
    "eslint-plugin-import": "^2.29.1",
    "eslint-plugin-n": "^16.4.0",
    "eslint-plugin-promise": "^6.1.1",
    "eslint-plugin-react": "^7.33.2",
    "postcss": "^8",
    "tailwindcss": "^3.3.0",
    "typescript": "^5.3.3"
  }
}
```

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-5746287019974907"
     crossorigin="anonymous"></script>
<!-- 수평형 광고 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-5746287019974907"
     data-ad-slot="8862563347"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

### eslint rule 필수 항목 및 rule 사용법 알아보기 {#toc2}

- 위의 eslint 설정을 진행하고나면 현재 nextjs 프로젝트 위치에 .eslintrc.json 파일이 자동으로 생성됩니다.

```json
// 프로젝트폴더위치/.eslintrc.json
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "standard-with-typescript",
        "plugin:react/recommended"
    ],
    "parserOptions": {
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
    }
}
```

- 이제 부턴 위의 .eslintrc.json 에 rules 에 문법 체크 규칙을 정의 해서 사용하게 될 텐데요.
- 사용하는 방식은 자유이지만, 처음 사용하게 되면, 조금 당황스러운 문법 규칙들로 인해 적응하기 전에 벌써 괜히 설치했다..
- 라는 생각이 들 수 있어 몇가지 알아두셔야 할 규칙을 설명 드리겠습니다.
- 그 이후에는 개발자 입장에서 고려할 만한 규칙들 목록을 적어 두었으니 참고하시기 바랍니다.

#### Missing return type on function.eslint @typescript-eslint/explicit-function-return-type

- 함수의 리턴 타입을 명시해야 한다는 규칙입니다.
- 함수의 리턴 타입을 명시하는것은 좋은 습관 같아 사용중입니다.

```js
// 규칙에 어긋나는 소스
export default function Home () {

// 규칙에 부합하는 소스
export default function Home (): ReactNode {
```

#### 'React' must be in scope when using JSX eslint(react/react-in-jsx-scope)

- eslint 를 설치하자마자 마주치게 되는 rule 입니다.
- 설치 전에는 정상으로 표시되던 소스가 갑자기, 에러로 표시되거든요.

```js
// 규칙에 어긋나는 소스
var Hello = <div>Hello {this.props.name}</div>;

// 규칙에 부합하는 소스
import React from 'react';

var Hello = <div>Hello {this.props.name}</div>;

```

- 'React' must be in scope when using JSX' 규칙은 React 를 import 해서 사용해라 라는 뜻으로 들립니다.
- 여기서 고민은, nextjs 는 src/app/page.tsx 메인 화면 소스에 해당 소스를 생성하지 않았습니다.
- eslint 를 설치하지 않고 서버를 기동시키면 모두 정상적으로 나오구요.
- 결론은 nextjs 개발시 모든 화면에 'import React from 'react';' 라인 추가가 필요하지 않습니다.
- 해당 규칙을 disable 하여 비활성화 할 수 있습니다. 아래를 보시죠

```json
// 변경 전
// 프로젝트폴더위치/.eslintrc.json
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "standard-with-typescript",
        "plugin:react/recommended"
    ],
    "parserOptions": {
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
    }
}

// 변경 후
// rules 에 "react/react-in-jsx-scope": "off", 추가
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "standard-with-typescript",
        "plugin:react/recommended"
    ],
    "parserOptions": {
        "ecmaVersion": "latest",
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
      "react/react-in-jsx-scope": "off",
    }
}
```

- 해당 라인을 추가하고 저장하면 에러로 표시되던 내용이 모두 사라질 것입니다.

#### Missing space before function parentheses.eslint (@typescript-eslint/space-before-function-paren)

- 해당 규칙은 함수명 뒤에 공백을 주라는 규칙입니다.

```js
// 규칙에 어긋나는 소스
export default function Home(): ReactNode {

// 규칙 부합하는 소스
export default function Home (): ReactNode {
```

- 저는 해당 규칙을 비활성화 하기를 추천드려서 언급하였습니다.

```json
// 변경 후
// rules 에 "@typescript-eslint/space-before-function-paren": "off", 추가
{
  "env": {
    "browser": true,
    "es2021": true
  },
...생략
  "rules": {
    "react/react-in-jsx-scope": "off",
    "@typescript-eslint/space-before-function-paren": "off"
  }
}
```

#### All imports in the declaration are only used as types. Use `import type`.eslint @typescript-eslint/consistent-type-imports

- 해당 규칙은 import type 까지 적어서 사용하라는 의미입니다.
- 저는 해당 규칙을 좋아하지 않아 off 해놓고 사용합니다.

```json
// 변경 후
// rules 에 "@typescript-eslint/space-before-function-paren": "off", 추가
{
  "env": {
    "browser": true,
    "es2021": true
  },
...생략
  "rules": {
    "react/react-in-jsx-scope": "off",
    "@typescript-eslint/space-before-function-paren": "off",
    "@typescript-eslint/consistent-type-imports": "off"
  }
}
```

<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=ca-pub-5746287019974907"
     crossorigin="anonymous"></script>
<!-- 수평형 광고 -->
<ins class="adsbygoogle"
     style="display:block"
     data-ad-client="ca-pub-5746287019974907"
     data-ad-slot="8862563347"
     data-ad-format="auto"
     data-full-width-responsive="true"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>

### Prettier

- ESLint 와 함게 Prettier를 주로 사용하고 저도 사용중인데요.
- vscode 에서 두 가지 모두 사용하고 있고, 파일을 저장 할 때,
- 기본 Fomatter 로 Prettier를 사용하도록 하여 에러를 자동 수정하도록 사용중입니다.
- prettier 설정에 대한 자세한 정보는 제가 작성한 아래의 글을 참고하시기 바랍니다.
- :arrow_right: [prettier 사용법, configure(설정) 방법 안내](/nextjs/prettier-Tutorial-01-KR)

```json
//settings.json
{
    ...생략
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    ...생략
}
```

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

- 위의 예시를 기준으로 npm run format 을 하였을때, prettier 부분이 어떻게


- 끝까지 읽어주셔서 감사합니다.
- 아래의 ESLint 공식 문서에서 더 많은 정보를 찾아보실 수 있습니다.
- :arrow_right: [ESLint 공식 문서 사이트](https://eslint.org/docs/latest/)
- ESLint 에 이어 Prettier 에 대한 정보를 알고 싶으시다면, 제가 작성한 포스트를 참고하시기 바랍니다.
- :arrow_right: [prettier 사용법, configure(설정) 방법 안내](/nextjs/prettier-Tutorial-01-KR)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
