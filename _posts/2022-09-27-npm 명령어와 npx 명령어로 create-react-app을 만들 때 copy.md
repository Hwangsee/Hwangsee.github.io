---
title: 220927 TIL | npm 명령어와 npx 명령어로 create-react-app을 만들 때
tags: [React, TIL]
---


```jsx
npm install -g create-react-app song-search-project
```

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/19771d76-eef2-44a3-b96e-97bc727c4834/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221025%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221025T113412Z&X-Amz-Expires=86400&X-Amz-Signature=081b7fe5ead22254814f9e6ad4b694bb002d9d7944cb6f8fd631e377f207cafb&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

```jsx
hwang-yunjin@hwang-yunjin-ui-MacBookAir song-search-project % npx create-react-app song-search-project
```

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3104202b-93a8-4768-9e93-22c1f8f92ce7/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20221025%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20221025T113427Z&X-Amz-Expires=86400&X-Amz-Signature=1a8a96bb8876b58a7954a3426d302f67f505d0f4a34c9a32672a95accced0e44&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

npm 명령어는 안 되고, npx 명령어는 되는데 이 둘의 차이가 뭘까?

### 시도한 방법 1

리포지토리 레지스트리가 누락되었을 수 있다.

```jsx
$ npm set registry https://registry.npmjs.org/
```

리포지토리 등록 후 캐시 정리를 한다.

```jsx
$ npm cache clean
$ npm rebuild
```

결과 : 동일한 오류 메세지 발생 (실패)

### 시도한 방법 2

**npm create-react-app을 이미 전역적으로 설치했기 때문에 오류가 발생할 수 있다.**

전역적으로 설치한 상태라면, 프로젝트를 생성하기만 하면 된다.

```jsx
npm init react-app @프로젝트명
```

결과 : 성공!

**출처 :** 

[Getting Error 404 while running npm install create-react-app](https://stackoverflow.com/questions/54238540/getting-error-404-while-running-npm-install-create-react-app)

### 그렇다면 npm과 npx의 차이는 무엇일까?

[npm과 npx의 차이에 대해서](https://ljh86029926.gitbook.io/coding-apple-react/undefined/npm-npx)

## 내용 요약

### npm 명령어

npm 명령어로 모듈 설치 시 전역으로 모듈을 설치해야 매 프로젝트마다 모듈을 설치하지 않아도 됨.

**하지만 이는 좋은 방법이 아니다.**

전역 모듈 설치 시 모듈이 업데이트 되었는지, 아닌지 확인이 불가능하다.

업데이트 진행 시 변동사항이 생겨 다른 프로젝트에 영향을 끼칠 수 있다.

**create-react-app 같은 `보일러 플레이트`에는 치명적이다.**

> **보일러 플레이트**란?
**최소한의 변경으로 여러곳에서 재사용되며, 반복적으로 비슷한 형태를 띄는 코드.**
> 

### npx의 등장

npx는 npm 5.2 버전부터 기본 패키지로 제공되는 모듈의 일종이다.

### npx, 뭐가 좋나요?

npx를 통해 create-react-app 설치 시 **매번 최신 버전만을 가져와서 설치해 주기 때문에**, 지금 어떤 버전을 사용하고 있는지 신경쓸 필요가 없다. 어차피 최신 버전만 사용하니까.

npx로 프로젝트를 생성하여 마무리했다.