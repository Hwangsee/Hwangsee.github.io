---
title: 221026 TIL | createRef, useRef 차이
tags: [React, TIL]
---

실무에서 createRef를 사용하는 부분도 있고 useRef를 사용하는 부분도 있어 찾아 보았다.  
요약하자면  

- Class형 컴포넌트에서 `createRef`를 통해 ref를 사용
- 함수형 컴포넌트에서는 `useRef`를 사용

함수형 컴포넌트에서 `createRef`를 사용하면 리렌더링 될 때마다 ref 값이 초기화 되어 원하는 값을 얻지 못한다.
