---
title: 221026 TIL | createRef, useRef 차이
tags: [React, TIL]
---

실무에서 createRef를 사용하는 부분도 있고 useRef를 사용하는 부분도 있어 찾아 보았다.  

## Ref란?
- Ref는 render 메서드에서 생성된 DOM 노드나 React 엘리먼트에 접근하는 방법을 제공한다.

### 왜 Ref를 사용하나요?
- 바닐라 자바스크립트에서는 DOM 객체에 접근하기 위해서 `querySelector`, `getElementById` API를 사용했다.
- 리액트에서 데이터는 State로 조작되기 때문에 DOM API와 혼합해서 데이터 및 조작을 할 경우 **디버깅이 어려워지고, 유지보수가 어려운 코드**가 된다.
- 또한 map 메소드를 이용해 렌더링되는 리스트 형태의 Element는 같은 ID를 가지기 때문에 `querySelector`, `getElementById`로 요소를 판별하기 어렵다.
- 따라서 리액트는 DOM API를 이용한 컴포넌트 제어 대신 `ref`라는 기능을 제공하는 것이다. 
  - `ref`를 이용하면 DOM 객체에 대한 직접적인 참조 주소를 받아 HTML DOM 메소드 및 속성을 이용할 수 있다. 

> ref는 componentDidMount나 useEffect가 실행되기 전에 할당된다. 컴포넌트가 마운트 될 때 할당되고 언마운트 될 때 Null을 할당한다.

## 클래스 컴포넌트 = createRef, 함수형 컴포넌트 = useRef
-  `createRef`와 `useRef`는 공통적으로 current 속성을 가지는 Mutable Object를 반환한다.
- 클래스 컴포넌트는 인스턴스를 생성해, render 메서드를 호출하는 방식으로 리렌더링을 하고, 함수형 컴포넌트는 해당 함수를 **리렌더링마다 다시 실행**한다.
- 때문에 함수형 컴포넌트에서 `createRef`를 사용하면 리렌더링 될 때마다 ref 값이 초기화 되어 원하는 값을 얻지 못할 수 있다.

## useImperativeHandle와 ForwardRef
- 리액트는 기본적으로 Declarative(선언형) 패러다임을 따르지만 Ref 기능의 경우 기능을 조작하기 위해 Imperative(명령형) 패러다임을 따른다.



#### 출처
- [createRef와 useRef 그리고 useImperativeHandle](https://medium.com/react-native-seoul/react-%EB%A6%AC%EC%95%A1%ED%8A%B8%EB%A5%BC-%EC%B2%98%EC%9D%8C%EB%B6%80%ED%84%B0-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90-07-createref%EC%99%80-useref-%EA%B7%B8%EB%A6%AC%EA%B3%A0-useimperativehandle-2fb5445d168b)
- [createRef, useRef의 차이](https://kyounghwan01.github.io/blog/React/useRef-createRef/)