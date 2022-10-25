---
title: 221006 TIL | 동적 라우팅 - Query parameters, URL parameters
tags: [React, TIL]
---

> ❓  상품 판매 리스트 페이지 → 판매 상세 페이지로 이동했을 때
이전 리스트 페이지에서 어떤 상품을 클릭했는지 어떻게 알고 API를 호출하는 걸까?
> 

```jsx
// Routes.js
import React from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";

import Home from "./Pages/Home";
import List from "./Pages/List";
import Detail from "./Pages/Detail";

class Routes extends React.Component {
  render() {
    return (
      <Router>
        <Switch>
          <Route exact path="/" component={Home} />
          <Route exact path="/list" component={List} />
          <Route exact path="/detail" component={Detail} />
        </Switch>
      </Router>
    );
  }
}

export default Routes;
```

- 예제 코드는 각각 리스트 페이지와 디테일 컴포넌트로 이동하도록 되어 있지만, 어떤 상품에 대한 상세 페이지를 나타낼지 알 수 있는 방법이 없다.
- 이런 상황에서는 **유동 라우터**를 통해 디테일 페이지를 연결할 수 있다.

# 동적 라우팅

## 동적 라우팅이란?

- 라우트의 경로에 특정 값을 넣어 해당 페이지로 이동할 수 있게 하는 것. (=Dynamic Routing)
- React Router에서는 두 가지 방법을 통해 유동 라우팅 기능을 이용할 수 있다.
    1. `Quey parameter`
    2. `URL parameter`

## 라우트로 설정한 컴포넌트의 3가지 props

- 라우트로 설정한 커모넌트는 3가지의 props를 전달받게 된다.
1. history
    - 이 객체를 통해 push, replace를 통해 다른 경로로 이동하거나 앞, 뒤 페이지로 전환할 수 있다.
2. location
    - 현재 경로에 대한 정보, URL 쿼리(/company_list?category=3)에 대한 정보를 가진다.
3. match
    - 어떤 라우트에 매칭이 되었는지에 대한 정보가 있고 params(/company_detail/:id)에 대한 정보가 있다.

처음 리스트 페이지로 접근했을 때, 특정 카테고리에 해당하는 데이터를 화면에 보여주고 싶다면? Query Parameter를 사용한다.

## Query parameter

### Query parameter을 이용해 리스트 페이지 이동

- 백엔드에 호출 값을 보낼 때 디테일 데이터를 받고 싶으면 주소 뒤에 id 값을 보내줘 라고 요청했을 때
- url의 주소가 `/company_list?category=3` 인 경우

```jsx
const id = this.props.location.search.split("=")[1];

fetch(`${localhost}/job/list/${id}`, {
	// 생략
})
```

- this.props.location을 통해 해당 주소의 정보를 가지고 올 수 있다.
- id에는 3이라는 값이 담긴다.

### Query parameters가 길어지는 경우

- Query parameters는 & 연산자를 이용하여 얼마든지 길어질 수 있다.
- 11번가의 상세 페이지를 예시로 들어 보자.

```jsx
http://www.11st.co.kr/product/SellerProductDetail.tmall?method=getSellerProductDetail&prdNo=1259146705&trTypeCd=PW00&trCtgrNo=1001841
```

- 원하는 name의 value를 가져오기 위해서는 그에 맞는 함수를 작성하면 된다
    - name 값을 매개변수로 받고 switch문을 사용해 value를 return하는 함수를 작성하거나…

### 리스트 페이지 → 상세 페이지 이동할 때

- 리스트 페이지 안 CompanyItem 컴포넌트가 있고 map 함수를 통해 렌더링 중인 예시

```jsx
<CompanyItem
	...생략
	jobId={e.job.id}
/>
```

- CompanyItem 클릭 시 Link 전체가 Link 태그로 감싸져 있다고 할 때

```jsx
<Link to={`/company_detail/${this.props.id}`}>
</Link>
```

- 바로 여기서 URL Parameter를 사용할 수 있다.

## URL Parameter

- URL Parameter는 match라는 객체가 있다.
- match : 어떤 라우트에 매칭되었는지에 대한 정보가 있고 params(/company_detail/:id) 정보를 가지고 있다.
- params 정보는 this.props.match.params를 통해 가지고 올 수 있다.
    - hook을 사용할 경우 `useParams()`를 사용해도 된다.
- **params 데이터를 사용하기 위해서는 반드시 Route에서 값을 지정해주어야 한다.**

```jsx
// Routes.js로 돌아와서
<Router>
 <Switch>
		<Route exact path="/company_detail/:id" component={ComponentDetailPage}>
 </Switch>
```

- :는 Path Parameter가 올 자리를 의미한다.
- id는 Path Parameter의 변수명이다.

```jsx
// ComponentDetailPage.js
fetch(`${localhost}/job/detail/${this.props.match.params.id}`, {
	// 생략
})
```

- `this.props.match.params.id` 이렇게 id 값을 가져올 수 있다.

## 두 가지 방법은 각각 어떤 상황에서 사용하는 게 좋을까요?

### String parameters

- 해당 페이지에서 여러 정보가 필요한 경우
- Query Paramert를 사용하고 싶은 경우 routes.js를 수정해야 하기 때문에 유지보수 측면에서 좋지 않다고 판단될 경우
- 필터링을 하고 싶을 경우

### Query parameter

- 특정한 resource를 식별하고 싶을 경우, 딱! 하나만 필요할 경우.

### 요약

- SPA를 구축하기 위해 라우팅 기능이 필요하다면, React Router 라이브러리를 사용해 기능을 구현할 수 있다.
- 유동 라우터랑 라우트 경로에 특정 값을 넣어 해당 페이지로 이동할 수 있게 하는 것인데, React Router에서는 두 가지 방법을 통해 유동 라우팅 기능을 이용할 수 있다.
- 1) Query String, 2) URL Parameter

> 문서 정리 출처
> 

[https://velog.io/@joonsikyang/React-Project-URL-parameters-Query-parameters](https://velog.io/@joonsikyang/React-Project-URL-parameters-Query-parameters)