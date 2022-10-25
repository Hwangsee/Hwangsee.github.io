---
title: 220921 TIL | 웹소켓(WebSocket)이란?
tags: [Network, TIL]
---

# 웹 소켓

- HTTP 연결 방식은 요청과 그에 대한 응답이 있어야 하기 때문에 채팅 서비스, 알림을 구현하기 적합하지 않다.
- HTTP의 단점을 보완하기 위해 HTML5에서 등장한 것이 `웹 소켓(WebSocket)`이다.
- **웹 소켓(WebSocket)이란?** 서버와 사용자간 연결을 유지한 상태로 추가 요청 없이 양방향으로 데이터를 교환할 수 있는 프로토콜

ex ) 웹 소켓 생성 방법

```jsx
// 연결을 만들 때는 WebSocket 생성자를 이용한다.
// 매개변수로 사용하는 프로토콜은 ws, wss이다. (http, https 격으로 wss가 보안과 신뢰성 높음)
const socket = new WebSocket("wss://example.com");
```

- 웹 소켓의 핸드셰이크 과정은 HTTP를 이용한다.
  - HTTP는 1.1 버전 이상이어야 하며 GET 메서드를 이용하여 요청을 보낸다.

## WebSocket의 핸드셰이크 과정

1. 브라우저 → 서버 웹 소켓 지원 여부를 물어본다.
2. Connection 헤더와 Upgrade 헤더의 값을 설정한다.

   1. 클라이언트에서 프로토콜을 웹 소켓 프로톨로 변경하고 싶다는 의미

   ```jsx
   GET /chat HTTP/1.1
   Host: example.com
   Upgrade: websocket
   Connection: Upgrade
   ```

3. 서버에서 웹 소켓 지원 여부를 반환한다.
4. 서버, 브라우저 모두 웹 소켓을 지원한다면 연결이 생성된다.

## 웹 소켓의 readyState

- XMLHttpRequest로 생성된 객체처럼 각각의 상태를 나타내는 값이다.
- 상수, 혹은 숫자로 구분하여 사용할 수 있다.
- `CONNECTING (0)` : 연결이 수립되고 있는 상태
- `OPEN (1)` : 연결 수립된 상태
- `CLOSE (2)` : 연결 종료되고 있는 상태
- `CLOSED (3)` : 연결 종료

```jsx
const socket = new WebSocket("wss://example.com");

console.log(socket.readyState, WebSocket.CONNECTING);
```

- 웹 소켓이 정상적으로 생성되면 4개의 이벤트를 사용할 수 있다.
- 각 이벤트는 on<eventName>, addEventListener()를 통해 등록할 수 있다.
- `open` : readyState가 OPEN 되었을 때
- `close` : readyState가 CLOSED 되었을 때
- `message` : 서버로부터 메세지를 받았을 때, 이벤트 객체를 통해 수신된 메세지에 접근
- `error` : 에러가 발생했을 때