---
toc: true
title: "eslint 사용법, configure(설정) 방법 안내"
description: "이번 글에서는 eslint 사용법에 대해 다루어 보려 합니다. vscode extension 과 npm(yarn) install 을 통한 패키지 설치에 대한 부분도 언급하여 다루고 있습니다."
categories: [nextjs]
tags: [nextjs]
redirect_from:
  - /2023/12/20/
---

### eslint 사용법 {#toc1}

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

#### eslint 설정

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

#### eslint rule 필수 항목 및 rule 사용법 알아보기

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

- 이제 부턴 위의 .eslintrc.json 에 rules 키에 해당하는 value 를 설정하는 방법에 대해 설명드리겠습니다.



- :arrow_right: [nextjs 개발시 매우 도움이 되는 vscode extension 모음](https://marindie.github.io/nextjs/2023-12-14-Nextjs-Tutorial-03-KR)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
