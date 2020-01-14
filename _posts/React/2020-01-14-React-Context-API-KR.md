---
toc: true
title: "React: Context API 샘플"
description: "Context API 샘플 소스"
categories: [React]
tags: [React]
redirect_from:
  - /2020/01/14/
---

> React Context API 를 사용하여 prop drilling 이슈를 우회하는 방법을 설명합니다.
  개념은 간단하게, 사용방법에 초점을 마추어 코드 Syntax 관련된 부분만 적을 것입니다.

### 기본 개념 {#toc1}
```md
이걸 사용하는 목적은 단 하나입니다. 함수처럼 IN -> OUT 이 가능하도록 하기 위함입니다.
왜 이 단순한걸 못하냐구요? React 는 prop, state 같은 개념을 도입해서 Component 라는 이름안에
전달을 하는데.. 이때 Component 안에 더 작은 Component 들을 당연하게 설계 개념에서 도입을 하게 되면서
Tree 구조 처럼 가지가 뻗어 나가기 때문입니다.
그래서 Root -> Component1 -> Component 5 으로 까지 내려갈 수 도 있는데요..
var thisInfo 를 전달하려면, Root 가 준 prop 을 Component1,2,3,4 를 거쳐서 Component5 에 전달이 되는게 보통입니다.
참 희안한 방식이지요..
그래서 저는 React 를 그다지 쉽다고 생각하지 않았다가..... 이제 
이것을 해결해주는 Provider, Consumer 개념을 도입한 Context API 가 등장하면서
기존 프로그래밍과 비슷한 방식으로 사용이 가능해 졌습니다.

대부분의 예제를 검색하면 기존 방식 소스를 보여주고, Context API 개념을 보여주는데, 저는 그냥 
Context API 만... 귀찮아서..

그리고 소스를 바로 실무에 사용하기에는 데이터 부분이 많이 허접합니다.. Redux 나 Axios 를 사용한 예제가 아닙니다.

키포인트는, Provider 에 value 로 JSON 을 넣든 Text나 배열을 넣든 넣고, Consumer 에서 context 라는 키워드로 끄집어 내서 사용합니다.
MainProvider 에서 {this.props.children} 을 안 넣으니 에러가 났었습니다.. 초보적인 에러??
Single Dom Return 은 언제나 자주 실수하는 부분.. 
그외에 특별한 에러는 없었네요..
```
### Context API Provider Consumer {#toc2}
```js
//MainContext.js
import React from 'react';

const MainContext = React.createContext();

export default MainContext;

//MainProvider.js
import React from 'react';
import MainContext from '../context/MainContext';

class MainProvider extends React.Component{
    state = {
        categories: {
            "name01": "Oracle",
            "name02": "React"
        }
    };

    render(){
        return (
            <MainContext.Provider
                value={{
                    categories: this.state.categories
                }}
            >
                {this.props.children}
            </MainContext.Provider>
        )
    }
}

export default MainProvider;

//Main.js
import React from 'react';
import MainProvider from './MainProvider';
import Category from './Category';

class Home extends React.Component {
    render() {
      return (
          <MainProvider>
            <Category/> 
          </MainProvider>
      )
    }
}

export default Home;

//Category.js
import React, { Fragment } from 'react';
import MainContext from '../context/MainContext';

const Category = () => (
    <MainContext.Consumer>
        {context => (
            <Fragment>
                {Object.keys(context.categories).map(key => (
                    <p>{context.categories[key]}</p>
                ))}
            </Fragment>
        )}                
    </MainContext.Consumer>    
);

export default Category;

```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io