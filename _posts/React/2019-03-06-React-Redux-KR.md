---
toc: true
title: " Redux 개념 정리"
description: "Redux 개념 정리를 제가 이해하기 쉬운 방향으로 정리하였습니다."
categories: [React]
tags: [Redux]
redirect_from:
  - /2019/03/06/
---

> Redux 개념 정리를 제가 이해하기 쉬운 방향으로 정리하였습니다.

### Redux {#toc1}

제가 Tutorial 을 간단히 따라해 보고 한두번 해본 결과를 바탕으로 적은 내용입니다.
개발 경험이 부족해 내용이 상당히 미흡 할 수 있습니다.

제가 처음 React 를 배웠을 때의 샘플 소스는, Component Level 단의 State Management 였습니다.
constructor 에 일반적으로 변수를 정의 하고 setState 함수 사용 방식 또는, props 를 사용하여 접근하는 형태의 방식으로 Data를 전달하여 Rendering 에 변경사항이
발생하게 하였습니다.

Redux 는 React 에서 Application Level (앱 전역) 단에서 data 들을 들고 다니면서 사용하기 위해 만들어진 개념 같습니다.
개념적인 부분이 강한데, Spring MVC 의 개념을 받아들이고 사용하는 것과 조금 비슷하다고 보입니다. 
제가 사용한 경험을 바탕으로 썰을 풀자면, 기본적으로 알아야할 Syntax 들이 몇개 있는데, 다음과 같습니다.
Store, Action, Dispatch, Reducer, Connect, mapStateToProps...
이런 단어를 사용하면서 개념을 설명하는데, 저는 이해가 되지 않는 부분은 일단 패스 하고, 코딩하면서 느낀 부분만을 글로 간단히 적어 놓고 가끔 들여다 보려합니다.

제가 이해하기 쉬운 방향으로 정의한 순서는 다음과 같습니다.
Action, Dispatch, Connect 정의 => Reducer 정의 => Store 정의 => mapStateToProps 함수 사용

### Action 정의 {#toc2}

먼저 어떤 행위를 하고 싶은지 정의 하는것이 제일 코딩하기 쉬워보였습니다. 아래는 샘플 소스 postAction.js

```js

import { FETCH_POSTS, NEW_POST } from './types';

export const fetchPosts = () => dispatch => {
    console.log('fetchPosts');
    fetch('https://jsonplaceholder.typicode.com/posts')
    .then(response => response.json())
    .then(posts => dispatch({
        type: FETCH_POSTS,
        payload: posts
    }));  
};

export const createPost = postData => dispatch => {
    console.log('createPost');
    fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'POST',
      headers: {
        'content-type': 'application/json'
      },
      body: JSON.stringify(postData)
    })
    .then(res => res.json())
    .then(post => dispatch({
        type: NEW_POST,
        payload: post
    }));
};

```

types.js 는 보너스

```js

export const FETCH_POSTS = 'FETCH_POSTS';
export const NEW_POST = 'NEW_POST';
export const DELETE_POST = 'DELETE_POST';

```

해당 함수들을 정의 하고 component 내에서 사용하면 되는데, connect 와 함께 같이 정의 해야 정상 동작합니다.
밑에서 다시 얘기하고, 예시만 남깁니다.
아래는 예시

```js

//소스일부 생략...

  componentWillMount(){
    this.props.fetchPosts();
  }

//소스일부 생략...

const mapStateToProps = state => ({
  posts: state.posts.items,
  newPost: state.posts.item
});

export default connect(mapStateToProps, { fetchPosts })(Posts)  
```

좀 헷갈렸던 부분은, 어떤 액션을 하고자 하여 FETCH_POSTS 를 정의 하였다면, 그에 대한 로직을 정의한 함수를 만들어서 사용하게 되는데, 
해당 결과를 Application Level 단에서 사용하기 위해 어떤 방식을 사용하는것 같다는게 저의 생각입니다. 그걸 담는 곳이 Store 이고,
그 Store 안에 Reducer 들을 필요한 대로 정의 하여 서술해 놓으면, 어떤 컴포넌트에서든, 해당 선언 이름으로 reducer 를 호출하여
그 안의 Data 를 바탕으로 뭔가를 할 수 있는것 같습니다. 

그렇다면, Store 에 Reducer 를 선언하고, 해당 reducer 를 사용하는 소스가 어떻게 되어있는지 Reducer 와 Store 정의 부분을 이어서 보겠습니다.

### Reducer 정의 {#toc3}

Reducer 는 Type 과 Data에 대한 매핑 정의를 통해 각 Component 들이 필요로 하는 Data 에 대한 변경을 가능하게 해주는 녀석으로 이해했습니다.
reducers 폴더 안에 index.js 와 postReducer.js 를 만들어서 사용하였습니다. 왜 index.js 와 postReducer.js 를 만들었는지는 저도 잘 모릅니다.
다만 따라해 보고 이를 바탕으로 코딩해 보았더니, index.js 에 Application Level 단에서 불러서 사용가능한 Reducer 이름들을 정의 하고, 
각 Reducer 의 내용은 별도로 만들어서 관리하는게 용이해 보여서 그런 것 같습니다.

아래는 index.js

```js

import { combineReducers } from 'redux';
import postReducer from  './postReducer';

export default combineReducers({
    posts: postReducer
});

```

그리고 postReducer.js

```js

import { FETCH_POSTS, NEW_POST } from '../actions/types';

const initialState = {
    items: [],
    item: {}
}

export default function(state = initialState, action) {
    switch(action.type) {
        case FETCH_POSTS:
            console.log('FETCH_POSTS');
            return {
                ...state,
                items: action.payload
            };
        case NEW_POST:
            console.log('NEW_POST');
            return {
                ...state,
                item: action.payload
            };            
        default:
            return state;
    }
}

```

postReducer.js 에 기술된 내용은, 만약 어떤 Action 이 호출이 되어 처리가 되고나서 Dispatch 된 결과를 Parsing 하는 함수를 정의 한 것으로 보입니다.
이 녀석이 자동으로 포함 되어 처리가 되는 방식은 store.js 에 rootReducer 를 redecuers 폴더로 잡아 놓았기 때문이라고 생각됩니다.
Reducer 는 여러개가 될 수 있지만, 결국 rootRecuder 가 Tree node 의 최상위에 있고, 그 하위 node 의 개념으로 Custom Reducer 들을 업무에 필요에 따라
정의 하고 포함 시켜서 Application Level 단에서 사용하는 것 같습니다.

위의 예제의 경우에는 return 된 값 "action" 안에 있는 type 값이 FETCH_POSTS 이면, payload 에 정의된 값을 items 에 세팅 시켜주는 로직입니다.

그렇다면, items 를 component 에서 사용하려면 어떻게 해야 할가요? 일단 조금 고민하고 게시고... 위에 먼저 언급한 store에 reducers 들을 등록하는
부분인 Store 정의를 먼저 진행하겠습니다.

### Store 정의 {#toc4}

Store 는 Redux 개념에서 단일 객체 정도로 이해하면 될 것 같습니다. Redux 에서는 모든 Component 의 State 를 
Application Level 개념으로 끌어올려서 전역으로 사용이 가능하고, 통합 관리하는 개념으로 이해했습니다.
이 Store 안에는 여러개의 Reducer 들이 정의가 되어, 각 Reducer 들의 매핑 정의 관계를 통해서 각 Component 들이
필요로 하는 정보들의 갱신이 가능하도록 State 역할을 대신해 주는것 같습니다. 

아래는 store.js

```js

import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

const initialState = {};

const middleware = [thunk];

const store = createStore(
    rootReducer, 
    initialState, 
    compose(
        applyMiddleware(...middleware),
        window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
    )
);

export default store;

```

위의 import rootReducer 부분을 보면, 저는 rootReducer 를 정의 하지는 않았습니다만, 위 처럼 코딩을 하면, 이 App의 rootReducer 에 포함될 
Reducer 들은 reducers 폴더안에 있다 라고 정의를 하는것 같습니다.

store.js 의 메인 정의 내용은 createStore 함수 사용인데요. 이녀석이 Application Level 단에서 Component 들에게 전달 하기 위한, 전역변수 같은
것들 세팅하는 것으로 저는 이해 했습니다. 이 createStore 안에 rootReducer 를 넣고, rootReducer 에 포함되는 녀석에 대한 정보를
reducers 폴더에 정의 하여 놓았습니다.

그럼 이제, 결과값인 items 를 component 에서 사용하려면 어떻게 해야 할지를 보도록 하겠습니다.

### mapStateToProps 함수 사용 {#toc5}

아래는 Post.js Component

```js

import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { connect } from 'react-redux';
import { fetchPosts } from '../actions/postActions';

class Posts extends Component {
  componentWillMount(){
    this.props.fetchPosts();
  }

  componentWillReceiveProps(nextProps) {
    console.log(nextProps);
    if(nextProps) {
        this.props.posts.unshift(nextProps.newPost);
    }
  }

  render() {
    const postItems = this.props.posts.map(post => (
      <div key={post.id}>
        <h3>{post.title}</h3>
        <p>{post.body}</p>
      </div>
    ));
    return (
      <div>
        <h1>Posts</h1>
        {postItems}
      </div>
    )
  }
}

Posts.propTypes = {
  fetchPosts: PropTypes.func.isRequired,
  posts: PropTypes.array.isRequired,
  newPost: PropTypes.object
}

const mapStateToProps = state => ({
  posts: state.posts.items,
  newPost: state.posts.item
});

export default connect(mapStateToProps, { fetchPosts })(Posts)

```

위에서 저는 아래의 소스를 index.js 에 코딩하였습니다

```js
export default combineReducers({
    posts: postReducer
});
```

이녀석이 의미하는 바는, posts 라는 이름을 전역으로 사용하겠다 라고 저는 이해했습니다.

그리고 Posts.js 에서 이녀석을 사용하기 위해 mapStateToProps 함수를 사용하여
{내가 Component 에서 사용할 이름}: state.posts.items 라는 소스를 구현하였습니다.
위에서는 posts: state.posts.items, 로 같은 이름을 사용하였지만, data: state.posts.items 로 바꿔도 무방합니다.

그럼 이제 Component 에 매핑된 posts에 접근하기 위해서는 어떻게 해야 할가요?
답은 this.props.posts 로 호출하면 Action 결과값인 items 의 내용이 담겨 있음을 확인 할 수 있습니다.

마지막으로, 이 액션을 호출 하려면 어떻게 해야 하는지도 알아야 겠지요?

답은 import { fetchPosts } from '../actions/postActions'; 로 import 를 하고
  componentWillMount(){
    this.props.fetchPosts();
  }
로 props 를 사용하여 호출하면 알아서 호출이 되었습니다.

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
