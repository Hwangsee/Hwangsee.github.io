---
title: 220920 TIL | 2022-09-20 async와 await, fetch
tags: [Network, TIL]
---

## async, await

- Promise 체이닝이 길어질 때는 ES2017 문법인 **async와 await 키워드**를 사용할 수 있다.
- 문법적 설탕이지만, Promise 사용을 좀 더 편리하고 세련되게 처리할 수 있다.

### async

- 함수 앞에 붙인다.
- async가 붙을 경우 Promise를 반환한다는 의미가 된다.

```jsx
function a() {
  return Promise.resolve(1);
}

async function b() {
  return 1;
}

a().then((res) => {
  console.log(res);
}); // 1
b().then((res) => {
  console.log(res);
}); // 1

// a()와 b()는 작성 방법이 다르지만 같은 의미이다.
```

### await

- async 함수 내에서만 사용되며, 함수의 실행을 중지하고 Promise가 fullfilled 될 때까지 기다린다.
  > TC39에서 async 함수 없이 모듈의 최상단에서 await를 실행할 수 있는 top-level await 명세가 논의 중이다.

```jsx
/** async를 사용하지 않는다면 */
function getResult() {
  a()
    .then((resultFromA) => b(resultFromA))
    .then((resultFromA) => c(resultFromA))
    .then((resultFromA) => console.log(resultFromC));
}
/** async를 사용한다면 */
async function getResult() {
  const resultFromA = await a();
  const resultFromB = await b(resultFromA);
  const resultFromC = await c(resultFromB);

  console.log(resultFromC);
}

/** try..catch를 통한 에러 처리 */
function errFunc() {
  return Promise.reject(100);
}

async function func() {
  try {
    const response = await errFunc(); // reject
  } catch (err) {
    console.log(err);
  }
}
func();

/** 혹은 await는 Promise를 반환하기 때문에 catch() 메서드를 이용해 에러 처리를 할 수도 있다. */
func().catch((err) => {
  console.log(err);
});
```

**awiait, async 적용 예제 )**

```jsx
function asyncFunc(time) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(time);
    }, time);
  });
}

async function func() {
  const a = await asyncFunc(1500);
  const b = await asyncFunc(2500);

  console.log(a + b);
}

func(); // 5000
```

asyncFunc() 함수가 순차적으로 실행될 필요가 없다면 Promise.All()을 사용해도 된다.

```jsx
async function func() {
  const res = await Promise.all([asyncFunc(1500), asyncFunc(2500)]);
  console.log(res); // [1500, 2500]
}
```