---
toc: true
title: "nextjs 개발시 매우 도움이 되는 vscode extension 모음"
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
- prettier 설정에 대한 자세한 정보는 제가 작성한 아래의 글을 참고하시기 바랍니다.
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

- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 는 TypeScript를 먼저 prettier 로 코딩 구조를 재정렬 하고
- ESLint를 설치하면 Javascript 나 TypeScript 확장자 파일내에 개발자가 작성한 문법이 ECMA 기준에 부합한지 판단 하고 경고과 에러로 표시가 뜹니다.
- 여러가지 Rule 이라고 하는 규직을 기본값이 정해진 상태로 사용하게 되며
- 사용자가 설정 내용을 잘 아는 만큼 비활성화 활성화 선택을 통해 어느정도 커스터마이징 가능합니다.
- 이후 ESLint 로 한번 더 코딩 구조를 재정렬 해주는 역할을 하는 extension 입니다.

[![ESLint](/assets/images/screen/eslint.png)](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

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

#### GitLens

- [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) 는 git을 사용하는 사용자에게 커밋한 사용자, 날짜 등등의 상세한 정보를 알려 주어
- 팀 개발시 수정 내역 파악에 도움이 많이 되는 extension 입니다.

[![GitLens](/assets/images/screen/GitLens.png)](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

[![GitLens](https://raw.githubusercontent.com/gitkraken/vscode-gitlens/main/images/docs/hovers-current-line.png)](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

#### Git Graph

- [gitgraph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph) 는 git 커밋 내용을 시각화 하여 다양한 브랜치들의 커밋내용을
- 타임라인 형태로 이력 확인이 가능하도록 해주는 extension 입니다.
- Git 관련 GitLens 와 GitGraph 에 대한 별도 포스팅도 작성하였으니 참고하시면 좋을 것 같습니다.
- :arrow_right: [vscode GitLens, GitGraph 사용법 안내](https://marindie.github.io/nextjs/2023-12-19-vscode-git-usage-01-KR)

[![gitgraph](/assets/images/screen/gitgraph.png)](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)

[![gitgraph](https://github.com/mhutchie/vscode-git-graph/raw/master/resources/demo.gif)](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph)

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

#### indent-rainbow

- [indent-rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow) 는 아래 이미지와 같이 들여넣기 위치 파악을
- 하기 쉽도록 도와주는 Extension 입니다. 저는 이걸 사용하고 나서 조건문 분기 파악이 더 빨라진거 같았습니다.
  
[![indent-rainbow](/assets/images/screen/indent-rainbow.png)](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

[![indent-rainbow](https://raw.githubusercontent.com/oderwat/vscode-indent-rainbow/master/assets/example.png)](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow)

#### Auto Close Tag

- [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag) 는 HTML Closing Tag 자동완성 기능 extension 입니다.

[![Auto Close Tag](/assets/images/screen/auto_close_tag.png)](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)

[![Auto Close Tag](https://github.com/formulahendry/vscode-auto-close-tag/raw/HEAD/images/usage.gif)](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)

#### Auto Rename Tag

- [Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) 는 HTML Tag 이름 수정시 Closing Tag 까지 자동으로 수정해주는 extension 입니다.

[![Auto Rename Tag](/assets/images/screen/auto_rename_tag.png)](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)

[![Auto Rename Tag](https://github.com/formulahendry/vscode-auto-rename-tag/raw/HEAD/images/usage.gif)](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)

#### colorize

- [colorize](https://marketplace.visualstudio.com/items?itemName=kamikillerto.vscode-colorize) 는 CSS, SCSS 파일의 색상값을 입력하면
- 해당 Text의 백그라운드에 색상값에 해당하는 색을 보여주는 extension 입니다.

[![colorize](/assets/images/screen/colorize.png)](https://marketplace.visualstudio.com/items?itemName=kamikillerto.vscode-colorize)

[![colorize](https://github.com/formulahendry/vscode-auto-rename-tag/raw/HEAD/images/usage.gif)](https://marketplace.visualstudio.com/items?itemName=kamikillerto.vscode-colorize)

#### 내용 정리

- 여기까지가 제가 강력히 추천하는 extension 들 이였습니다.
- indent-rainbow, auto close tag, auto rename tag, colorize extension 들은 별도의 설명이 필요 없습니다.
- 기능이 매우 직관적이니까요.
- Prettier 와 Eslint 는 추가 설명이 필요해 보여 별도의 포스팅을 작성하였습니다.
- :arrow_right: [eslint 사용법, configure(설정) 방법 안내](https://marindie.github.io/nextjs/2023-12-19-eslint-Tutorial-01-KR)
- :arrow_right: [prettier 사용법, configure(설정) 방법 안내](https://marindie.github.io/nextjs/2023-12-19-prettier-Tutorial-01-KR)
- Git 관련 GitLens 와 GitGraph 에 대한 별도 포스팅도 작성하였으니 참고하시면 좋을 것 같습니다.
- :arrow_right: [vscode GitLens, GitGraph 사용법 안내](https://marindie.github.io/nextjs/2023-12-19-vscode-git-usage-01-KR)
- 참고로 아래의 extension 은 완전 추천까지는 아닙니다.

#### ES7+ React/Redux/React-Native snippets

- [react-snippets](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets) 는 자주 사용하는 코드 블럭을 간단한 키워드로
- 세팅하여 해당 키워드를 입력후 엔터를 입력하면, 자동완성 기능이 동작하면서 개발자가 예측가능한 코드 블럭을 생성해주는 extension 입니다.
- 이를 snippet 이라고 부르는데요, 해당 extension 의 snippet 목록에 대한 정보는 아래의 링크를 클릭하시면 확인 하실 수 있습니다.
- :arrow_right: [react-snippets-list](https://github.com/r5n-dev/vscode-react-javascript-snippets/blob/HEAD/docs/Snippets.md)

[![react-snippets](/assets/images/screen/react-snippets.png)](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)

#### 설치 하지 않았으면 하는 extension

- Sublime Text Keymap and Settings Importer
- intellicode
- 위 두개의 extension 은 기존 vscode 단축키 세팅을 해당 extension의 키 세팅으로 엎어쳐서 vscode 기본 단축키의 활용도를 떨어트립니다.

```md
끝까지 읽어주셔서 감사합니다.
해당 내용이 도움이 되었으면 좋겠네요.
``````

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
