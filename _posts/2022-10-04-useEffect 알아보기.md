---
title: 221004 TIL | useEffect 알아보기
tags: [React, TIL]
---

## UseEffect()란?

- 리액트의 Hook이다.
- useEffect를 사용하면 컴포넌트가 마운트(처음 나타났을 때), 언마운트(사라질 때), 업데이트 됐을 때(특정 props가 바뀔 때) 특정 작업을 처리할 수 있다.

## 마운트 / 언마운트

- 첫번째 파라미터에 함수, 두번째 파라미터에 의존 값이 들어있는 배열(deps)를 넣는다.
- 배열을 비운다면 컴포넌트가 처음 나타날 때만 `useEffect`에 등록한 함수가 호출된다.
- useEffect에서 반환하는 함수는 `cleanup` 함수라고 한다.
- `cleanup` 함수는 useEffect의 뒷정리를 담당하는데, deps가 비어있다면 컴포넌트가 사라질 때 호출된다.

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log('컴포넌트가 화면에 나타남');
    return () => {
      console.log('컴포넌트가 화면에서 사라짐');
    };
  }, []);
  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => onToggle(user.id)}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => onRemove(user.id)}>삭제</button>
    </div>
  );
}

function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map(user => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}

export default UserList;
```

### 🔨 마운트 시 자주 하는 작업들

- props로 받은 값을 컴포넌트 로컬 상태로 설정
- 외부 API 요청 (REST API 등)
- 라이브러리 사용 (D3, Video.js 등)
- setInterval을 통한 반복작업 혹은 setTimeout을 통한 작업 예약

### 🔧 언마운트 시 자주 하는 작업들

- setInterval, setTimeout을 사용하여 등록한 작업들 clear하기
- 라이브러리 인스턴스 제거

## deps에 특정 값 넣기

- 특정 값을 넣으면 처음 마운트 됐을 때와 지정한 값이 바뀔 때 호출이 된다.
    - deps 안에 특정 값이 있다면 언마운트 시에도 호출이 되고, 값이 바뀌기 전에도 호출이 된다.
- `useEffect 안에서 사용하는 상태나 props가 있다면 useEffect의 deps에 넣는 것이 규칙이다.`
    - deps에 넣지 않는다면 useEffect 함수 실행 시 최신 props, 상태를 가르키지 않게 된다.

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log('user 값이 설정됨');
    console.log(user);
    return () => {
      console.log('user 가 바뀌기 전..');
      console.log(user);
    };
  }, [user]);
  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => onToggle(user.id)}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => onRemove(user.id)}>삭제</button>
    </div>
  );
}

function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map(user => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}

export default UserList;
```

## deps 파라미터를 생략한다면?

- 컴포넌트가 리렌더링 될 때마다 호출된다.

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log(user);
  });
  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => onToggle(user.id)}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => onRemove(user.id)}>삭제</button>
    </div>
  );
}

function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map(user => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}

export default UserList;
```