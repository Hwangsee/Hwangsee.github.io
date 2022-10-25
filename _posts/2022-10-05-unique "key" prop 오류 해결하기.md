---
title: 221005 TIL | unique "key" prop 오류 해결하기
tags: [React, TIL]
---

## Each child in a list should have a unique "key" prop 오류

- 리액트에서 배열을 렌더링 할 때에는 key 라는 props 를 설정해야한다.
- key 값은 각 원소들마다 가지고 있는 고유값으로 설정을 해야한다.
    - users 배열에서는 id가 고유 값이 된다.

```jsx

UserList.js
import React from 'react';

function User({ user }) {
  return (
    <div>
      <b>{user.username}</b> <span>({user.email})</span>
    </div>
  );
}

function UserList() {
  const users = [
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com'
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com'
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com'
    }
  ];

  return (
    <div>
      {users.map(user => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}

export default UserList;
```

- 만약 원소가 가지고 있는 고유한 값이 없다면 map() 콜백함수의 두번째 파라미터 index 를 key 로 사용하면 된다

```jsx
<div>
  {users.map((user, index) => (
    <User user={user} key={index} />
  ))}
</div>
```

- 경고 메시지가 뜨는 이유는, 각 고유 원소에 **key 값이 있어야만 배열이 업데이트 될 때 효율적으로 렌더링** 될 수 있기 때문이다
    - 리액트 라이브러리는 key prop을 사용하여 컴포넌트와 DOM 요소 간의 관계를 생성하고, 이 관계를 통해 컴포넌트 리렌더링 여부를 결정한다.
- 따라서 배열 안에 중복되는 key가 있거나 key 값이 없을 때 렌더링 시 오류 메세지가 콘솔 창에 출력되며, 업데이트가 효율적으로 이루어지지 않게 된다.

### key값은 어떤 값을 넣어야 할까?
- 자바스크립트의 배열은 정적이지 않기 때문에 **배열의 index를 key prop으로 사용하는 것은 지양**해야 한다.
- 배열의 원소의 순서가 바뀌면 index도 바뀌고 컴포넌트마다 고유해야 하는 key 값도 바뀐다.
  - 이는 리렌더링 시 잘못된 컴포넌트를 리렌더링 오류를 발생시킬 수 있다.
- key 값은 전역적으로 고유할 필요는 없으며 형제 요소에서 고유해야 한다.

> 출처  
[벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/basic/11-render-array.html)