---
title: 221003 TIL | 리액트로 투두리스트 만들기
tags: [React, TIL]
---

# Context API와 Redux 비교하기

## 전역 상태 관리는 언제 하면 좋을까?

- React의 useState를 이용하면 지역 상태 관리를 할 수 있다.
- 상태 값은 지역 상태관리를 사용하는 컴포넌트 안,
혹은 props로 전달할 때만 하위 컴포넌트로 사용할 수 있다.
- 모든 상태 관리를 useState만을 이용하여 진행할 수도 있지만,
여러 컴포넌트에서 사용되는 상태라면 props로 하나씩 상태 값을 내려주기에는 한계가 있다.
- 이 때 사용할 수 있는 전역상태관리 도구가 Context API, Redux이다.

## Context API의 특징

- React 내장기능이기 때문에 리액트에서만 사용할 수 있다.
- Entry 파일(root)에서 구성한 Provider를 내려주는 형식이다.
- 사용하고자 하는 컴포넌트에서 작성한 Dispatch와 State를 꺼내서 사용한다.
- reducer를 여러 개 만들면 Provider에서 여러 단계로 만들어 사용할 수 있다.

## Context API 사용 방법

1. Context 생성
    - createContext()를 통해 상태 저장
    - createContext에는 초기 상태 값을 넣을 수 있다.
    
    ```jsx
    import { crateContext } from 'react';
    const GameBoardStateContext = createContext({});
    ```
    
2. Action 생성
    - 액션을 지정하여 Reducer에서 서로 다른 타입일 때 다른 로직을 진행시킬 수 있다.
    
    ```jsx
    export type Action = { type : "UPDATE"; payload };
    ```
    
3. Reducer 생성
    
    ```jsx
    import { Action } from "@/actions/gameBoardAction";
    import { GameBoardState } from "@/contexts/gameBoardContext";
    
    const gameBoardReducer = (state: GameBoardState, action: Action): GameBoardState => {
      switch (action.type) {
        case "UPDATE":
          return {
            ...action.payload,
            // 변경되는 payload 로직 작성
          };
        default:
          throw new Error("Unhandled action");
      }
    };
    ```
    
4. Provider 생성
    - 중첩된 Provider로 여러 상태를 넣을 수 있다.
    
    ```jsx
    export const ContextProvider = ({ children }: { children: React.ReactNode }) => {
      const [gameBoard, gameBoardDispatch] = useReducer(gameBoardReducer, initState);
    
      return (
        <GameBoardStateContext.Provider value={gameBoard}>{children}</GameBoardStateContext.Provider>
      );
    };
    ```
    
5. Entry 파일에 적용
    
    ```jsx
    const App = () => {
      return (
        <>
          <ContextProvider>// 하위 컴포넌트들</ContextProvider>
        </>
      );
    };
    ```
    
    - 하위 컴포넌트에서 상태를 사용할 때는 useContext를 사용해 state 값을 꺼낼 수 있다.
        - 💡  useContext를 직접 사용하는 것보다 커스텀 Hook을 사용하는 방법이 좋다!
    
    ```jsx
    import { useContext } from "react";
    
    const state = useContext(GameBoardStateContext);
    ```
    

## Redux의 특징

- React 뿐만아니라 Vue 등 여러 프레임워크에서 사용할 수 있다.
- 상태를 저장하는 Store를 별도로 가지고 있다.
- thunk, saga와 같은 미들웨어를 추가적으로 사용하여 구성할 수 있다.
- Redux devtool extension을 사용하면 상태에 대한 디버깅이 가능하다.
- 전역 상태 관리 외에도 로컬 스토리지 상태저장, 버그 리포트 첨부 기능 등의 기능들을 사용할 수 있다.

## Redux의 사용 방법

1. Action 생성
    - 상태가 변하는 것에 대한 액션을 함수로 작성한다.
    
    ```jsx
    export const focusChange = (payload) => {
    	return {
    		type: "focusChange",
    		payload,
    	};
    }
    ```
    
2. Reducer 생성
    - 초기 상태를 설정하고 Action을 type으로 구별하여 상태를 업데이트하는 로직을 Reducer로 작성한다.
    
    ```jsx
    const initialState = {
    	startDate: null,
    	endDate: null,
    	focusedInput: START_DATE,
    };
    
    const datePickerReducer = (state = initialState, action) => {
    	switch (action.type) {
    		case "focusChange":
    			return { ...state, focusedInput: action.payload }
    		default:
    			return state;
    	}
    };
    ```
    
3. Store 생성
    - redux의 `createStore`를 통해 store의 인자에 생성한 Reducer를 넣어 생성한다.
    - 만약 Reducer가 여러 개라면 combineReducer를 통해 Reducer를 하나로 합치는 과정을 추가로 진행한다
    
    ```jsx
    import { createStore } from "redux";
    const store = createStore(datePickerReducer);
    ```
    
4. react-redux의 Provider로 root에 store 등록
    - Entry 파일에서 Provider에 store를 등록한다.
    
    ```jsx
    import { Provider } from "react-redux";
    ReactDOM.render(
    	<Provider store={store}>
    		<App />
    	</Provider>
    );
    ```
    
5. useSelector로 개별 컴포넌트에서 사용
    
    ```jsx
    import { useSelector } from "react-redux";
    const {
    	datePickerReducer: { startDate, endDate },
    } = useSelector((state) => state);
    ```
    

## 그렇다면 Context API와 Redux 중 어떤 걸 사용하면 되나요?

- Context API와 Redux는 사용법과 구조에 차이가 있을 뿐 전역 상태를 관리한다는 점이 유사하다.
    - Redux가 Context API를 기반으로 만들어졌기 때문이다.
- 단순 전역 상태 관리만 있어도 된다면 Context API, 디버깅이나 로깅 드의 상태 관리 외의 기능이나 미들웨어가 필요하다면 Redux를 사용하는 것이 좋다.
- Context API는?
    - React를 사용할 때 추가 dependency 없이 사용할 수 있어서 가볍게 사용할 수 있다.
    - 상태를 넘겨줄 때 상태가 여러 개라면 Provider를 중첩해서 내려줘야 하기 때문에 불편함이 있다.
- React는?
    - saga, thunk와 같은 미들웨어를 추가적으로 사용할 수 있어 비동기 처리를 따로 Util로 처리할 수 있는 장점이 있다.
    - 추가 설정을 통해 디버깅을 할 수 있다.
    - 미들웨어를 사용하기 위한 개념을 이해해야 하기 때문에 어려운 점이 있다.

### 결론

- 전역 상태 관리만 원한다면 Context API를 사용한다.
- 상태 관리 외 여러 기능이 필요하다면 Redux를 사용한다.
- Context API는 high-frequency updates에 좋지 않은 성능을 보인다. high-frequency 어플리케이션은 Redux를 사용한다.
  

> 출처
[https://egg-programmer.tistory.com/281](https://egg-programmer.tistory.com/281)
>