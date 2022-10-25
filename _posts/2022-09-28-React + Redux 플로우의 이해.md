---
title: 220928 TIL | React + Redux 플로우의 이해
tags: [React, TIL]
---

### 플로우 전체도

![Untitled](https://longtimehwang.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9b6a7f0a-ea26-42c8-8523-d41759974464%2FUntitled.png?table=block&id=49e15698-f265-4601-9c36-83e012db5987&spaceId=c74fdac9-4f8e-41de-9dca-c5af3bc54cba&width=1940&userId=&cache=v2)

## Store

- 상태는 기본적으로 store에서 집중관리
- Store는 커다란 JSON 결정체
- 규모가 클 경우 상태를 카테고리별로 분류한다.

```json
{
    // 세션과 관련된 것
    session: {
        loggedIn: true,
        user: {
            id: "114514",
            screenName: "@mpyw",
        },
    },

    // 표시중인 타임라인에 관련된 것
    timeline: {
        type: "home",
        statuses: [
            {id: 1, screenName: "@mpyw", text: "hello"},
            {id: 2, screenName: "@mpyw", text: "bye"},
        ],
    },

    // 알림과 관련된 것
    notification: [],
}
```

## ****Action 및 Action Creator****

- **Store 및 Store에 존재하는 State는 아주 신성한 것** 👼🏻
    - React 컴포넌트 같은 하등한 것이 직접 접근하려고 하면 안 됨
    - 접근하기 위해서는 Action이라는 의식을 거쳐야 한다. (이벤트 드리븐과 같은 개념)
1. Store에 대해 뭔가 하고 싶을 경우 Action을 발행
2. Store의 문지기가 Action의 발생을 감지하면 Store가 갱신
- Action은 아래와 같은 포맷을 갖고 있는 오브젝트

```json
{
    type: "액션의 종류를 한번에 식별할 수 있는 문자열 혹은 심볼",
    payload: "액션의 실행에 필요한 임의의 데이터",
}
```

ex ) 카운터의 값을 2배 늘리는 경우의 오브젝트

`@@myapp/` 이라고 Prefix을 붙인건 다른 사람이 쓴 코드와의 충돌을 피하기 위함

```json
{
    type: "@@myapp/ADD_VALUE",
    payload: 2,
}
```

하지만 이런 오브젝트를 만드는 것을 수작업으로 하는 건 정말 괴로운 일일 것이다…

또 “@@myapp/ADD_VALUE” 같이 매번 Action명을 문자열로 쓰는 것도 싫다.

그래서 이 작업을 편하게 하기 위해 일반적으로 상수와 함수를 준비하고 있다.

외부 파일이 참고할 수 있으니 export 하는 방법이 좋겠다.

![Untitled](https://longtimehwang.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbde29484-e1ec-402f-b35c-416d44865c8b%2FUntitled.png?table=block&id=2019a616-bd9e-4ddc-a2ad-29fac01c81c8&spaceId=c74fdac9-4f8e-41de-9dca-c5af3bc54cba&width=1940&userId=&cache=v2)

```jsx
export const ADD_VALUE = '@@myapp/ADD_VALUE';
export const addValue = amount => ({type: ADD_VALUE, payload: amount});
```

## Reducer

Store의 문지기와 비슷한 역할을 하는 것이 바로 Reducer이다.

Reducer는 Redux에 한해서 이전 상태와 Action을 합쳐 새로운 State를 만드는 조작이다.

![Untitled](https://longtimehwang.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F84c041da-9659-457e-9fd7-84e508c3e5b7%2FUntitled.png?table=block&id=9bb5d04a-4bf5-414d-b078-623856d0ba2d&spaceId=c74fdac9-4f8e-41de-9dca-c5af3bc54cba&width=1940&userId=&cache=v2)

```jsx
import { ADD_VALUE } from './actions';

export default (state = {value: 0}, action) => {
    switch (action.type) {
        case ADD_VALUE:
            return { ...state, value: state.value + action.payload };
        default:
            return state;
    }
}
```

⚠️ 주의해서 봐야할 것은 두가지이다.

- 초기 상태는 Reducer의 디폴트 인수에서 정의된다.
- 상태가 변할 때 전해진 state는 그 자체의 값으로 대체되는 것이 아닌, 새로운 것이 합성되는 것처럼 쓰여진다.

반환된 state는 store에 바로 반영되어 아래와 같이 변화한다.

```jsx
{
  value: 2,
}
```

트위터에서처럼 대규모 개발에 Reducer를 미세하게 분할하는 경우 Redux에서 제공하는 combineReducers 함수를 이용하여 아래와 같이 쓴다.

- combineReducers() : 앱이 복잡해질 때 사용. 여러 하위 상태들을 단일 상태 객체로 수집한다.

```jsx
import { combineReducers } from 'redux';

const sessionReducer = (state = {loggedIn: false, user: null}, payload) => {
    /* 생략 */
};
const timelineReducer = (state = {type: "home", statuses: []}, payload) => {
    /* 생략 */
};
const notificationReducer = (state = [], payload) => {
    /* 생략 */
};

export default combineReducers({
    session: sessionReducer, // session 및 sessionReducer가 관리하는 상태
    timeline: timelineReducer, // timeline 및 timelineReducer가...
    notification: notificationReducer,
})
```

이렇게 하면 Reducer 분할에 쓰인 Key가 그대로 State 분할에도 쓰인다.

실제로 각각 reducer의 정의 자체도 다른 파일로 나누는 것이 일반적이다.

## 순수한 Component와 연결된 Component

React의 Component 자체는 Redux의 흐름에 타는 것이 불가능하다.

흐름에 타기 위해서는 ReactRedux에 의해 제공되는 connect라고 불리는 함수를 이용하여 아래와 같이 쓴다.

함수판과 클래스판 각각 쓴다.

![Untitled](https://longtimehwang.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F37b03fed-4ae8-40e6-b882-1d7fe7580bdc%2FUntitled.png?table=block&id=4d542561-c7c2-41ab-abda-8f50928c33b3&spaceId=c74fdac9-4f8e-41de-9dca-c5af3bc54cba&width=1940&userId=&cache=v2)

```jsx
>>>>>>>> 함수판
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { addValue } from './actions';

const Counter = ({ value, dispatchAddValue }) => (
    <div>
        Value: {value}
        <a href="#" onClick={e => dispatchAddValue(1)}>+1</a>
        <a href="#" onClick={e => dispatchAddValue(2)}>+2</a>
    </div>
);

export default connect(
    state => ({ value: state.value }),
    dispatch => ({ dispatchAddValue: amount => dispatch(addValue(amount)) })
)(Counter)
>>>>>>>>> 클래스판
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { addValue } from './actions';

class Counter extends Component {
    render() {
        const { value, dispatchAddValue } = this.props;
        return (
            <div>
                Value: {value}
                <a href="#" onClick={e => dispatchAddValue(1)}>+1</a>
                <a href="#" onClick={e => dispatchAddValue(2)}>+2</a>
            </div>
        );
    }
}

export default connect(
    state => ({ value: state.value }),
    dispatch => ({ dispatchAddValue: amount => dispatch(addValue(amount)) })
)(Counter)
```

*코드를 보면 굉장히 복잡해보이지만…이해하고 나면 별 것 아니기 때문에 초조해하지 말고 냉정하게 바라보자…*

- Component가 Store로부터 정보를 받는 경우, props를 통해 받는다.
- props는 immutable하다.
    - 상태가 변경될 때마다 새로운 Compnent가 다시 만들어진다는 의미이다.

connect를 실행하고 있는 주변 코드들을 보자

1. Store가 가진 state를 어떻게 props에 엮을지 정한다. (이 동작을 정의하는 함수를 mapStateToProps라고  부른다.)
2. Reducer에 action을 알리는 함수 dispatch를 어떻게 props에 엮을지 정한다. (이 동작을 정의하는 함수를 mapDispatchToProps라고  부른다.)
3. 위 두가지가 적용된 props를 받을 Component를 정한다.
4. Store와 Reducer를 연결시킬 수 있도록 만들어진 Component가 반환값이 된다.

connect(mapStateToProps, mapDispatchToProps)(Component)라고 쓰인 걸 보면 좀 독특하다고 생각할 수 있겠지만, 최종적인 반환값은 4번과 같다.

### mapStateToProps

인수로 전달된 state는 전체를 의미한다.

```jsx
{
  value: 2,
}
```

가

```jsx
<Counter value={2} />
```

로 들어가길 바라며 state ⇒ (value: state.value)라고 작성

다른 프로퍼티가 없기 때문에 state ⇒ state라고 써도 동작에 무리가 없지만,

**기본적으로 필요한 것만 선별하여 props로 엮는 것**이 원칙이다.

### ****mapDispatchToProps****

Action Creater에서 action을 만든다고 해도 아무 일도 일어나지 않는다.

Reducer를 향해 통지를 할 수 있게 만들기 위해서는!! 만든 action을 dispatch라는 함수에 넘겨줘야 한다.

이렇게 하면 **모든 Reducer가 실행**된다. Reducer에 switch문으로 분기를 나눈 것은 바로 이 때문이다.

Reducer는 관계없는 action을 무시하고, 자기에게 주어진 action만을 처리하도록 되어 있어야 한다.

**또 Component 쪽에 하나하나 수동으로 dispatch 하는 처리를 하지 않아도 되도록, 여기서 action의 생성부터 dispatch의 실행까지 한번에 이뤄질 수 있도록 함수를 정의하여 props에 넘겨주도록 한다**는 멋진 의의도 알 수 있다.

### bindActionCreators

mapDispatchToProps를 이용하여 위와 같은 코드를 짜는 것에서 도망칠 수도 있다.

bindActionCreators라는 함수를 제공하며, 아래와 같이 생략이 가능하다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/79d5bde8-4f25-433f-a9ea-53b57fcc58f4/Untitled.png)

```jsx
import React, { Component } from 'react';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';
import { addValue } from './actions';

const Counter = ({ value, addValue }) => (
    <div>
        Value: {value}
        <a href="#" onClick={e => addValue(1)}>+1</a>
        <a href="#" onClick={e => addValue(2)}>+2</a>
    </div>
);

export default connect(
    state => ({ value: state.value }),
    dispatch => bindActionCreators({ addValue }, dispatch)
)(Counter)
```

현재는 bindActionCreatorsd의 실행도 생략할 수 있게끔 되어 있다.

```jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { addValue } from './actions';

const Counter = ({ value, addValue }) => (
    <div>
        Value: {value}
        <a href="#" onClick={e => addValue(1)}>+1</a>
        <a href="#" onClick={e => addValue(2)}>+2</a>
    </div>
);

export default connect(
    state => ({ value: state.value }),
    { addValue }
)(Counter)
```

*이렇게 해도 실행이 되는구나..*

## Container

연결된 Component를 Container라고 불렀지만, Container라고 불리는 Component들도 존재한다. 

- 수많은 Component가 리스트 형식으로 모여 있는데 가운데 각 요소의 Component를 각각 연결하면 수습이 안 되기 때문에 대표적인 자식 요소를 안고 하나의 부모 Component가 connect 되는 경우

```jsx
<UsersList>
    <User />
    <User />
    <User />
    <User />
</UsersList>
```

**대표로서 connenct될 부모 Component를 Container라고 부른다.**

Container는 가독성을 높이기 위해 Component와 다른 디렉토리로 나누는 경우가 많다.

![Untitled](https://longtimehwang.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F79d5bde8-4f25-433f-a9ea-53b57fcc58f4%2FUntitled.png?table=block&id=e04db5dd-24fc-4782-beba-b86b9b0789dd&spaceId=c74fdac9-4f8e-41de-9dca-c5af3bc54cba&width=1940&userId=&cache=v2)

> 출처
> 

[아마 이게 제일 이해하기 쉬울걸요? React + Redux 플로우의 이해](https://medium.com/@ca3rot/%EC%95%84%EB%A7%88-%EC%9D%B4%EA%B2%8C-%EC%A0%9C%EC%9D%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-%EC%89%AC%EC%9A%B8%EA%B1%B8%EC%9A%94-react-redux-%ED%94%8C%EB%A1%9C%EC%9A%B0%EC%9D%98-%EC%9D%B4%ED%95%B4-1585e911a0a6)