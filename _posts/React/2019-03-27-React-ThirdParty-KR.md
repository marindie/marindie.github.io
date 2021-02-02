---
toc: true
title: " React 에서 사용하는 3rd 파티 module 및 lib 정리"
description: "React 개발에서 사용되는 3rd 파티 module 및 lib 들에 대한 간략한 기능 정리 포스트입니다."
categories: [React]
tags: []
redirect_from:
  - /2019/03/27/
---

> React 개발에서 사용되는 3rd 파티 module 및 lib 들에 대한 간략한 기능 정리 포스트입니다.

### readux-thunk {#toc1}

```md
Action Creator 는 호출이 되면 바로 동작함. 그런데, Interval 이나 Interrupt 또는 Data 일부 처리 후 Event 호출이 필요할 때 있음.
이러한 것들을 가능하게 해주는것이 Thunk.
```

### auto-bind {#toc2}

```js
함수 호출시 해당 함수의 bind(this) 를 해주어야 하는 번거로움을 해결해줌.
주로 constructor 에서 정의 함.
이것도 귀찮아서 autobind-decorator 모줄을 install 하여 어노테이션 기반으로 Component Class 에 명시해서 사용중.

Ex)
@autobind
export class MasterListContainer extends Component {

```

### moment {#toc3}

### react-datepicker {#toc4}

```md
기본 적인 달력 표시용으로 사용합니다.
```

도움이 되었으면 좋겠네요. 감사합니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io