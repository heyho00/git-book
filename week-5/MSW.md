# MSW (Mock Service Worker)

[실습 레포](https://github.com/heyho00/frontend-survival-week04/tree/tdd)

코드 레벨이 아니라 프록시를 이용해 네트워크 레벨에서 가짜 구현.

오프라인 작업 등을 지원하기 위한 서비스 워커의 기능을 유용히 활용한 것.

## Service worker

서비스 워커를 활용해 네트워크 호출을 가로채 Mock Response를 주는 것.

<https://fe-developers.kakaoent.com/2022/221208-service-worker>

## 설치

```js
npm i -D msw
```

jest.config.js 파일의 'setupFilesAfterEnv' 속성에 setupTests.ts파일 추가.

```js
module.exports = {
 testEnvironment: 'jsdom',
 setupFilesAfterEnv: [
  '@testing-library/jest-dom/extend-expect',
  '<rootDir>/src/setupTests.ts', // 여기
 ],
 transform: {
  '^.+\\.(t|j)sx?$': ['@swc/jest', {
   jsc: {
    parser: {
     syntax: 'typescript',
     jsx: true,
     decorators: true,
    },
    transform: {
     react: {
      runtime: 'automatic',
     },
    },
   },
  }],
 },
};
```

파일도 만들어준다.

```js

// src/mocks/server.ts
// 따로 eslint 잡아줄 수 있지만 일단 이렇게 잡는다.
// eslint-disable-next-line import/no-extraneous-dependencies
import { setupServer } from 'msw/node';
import handlers from './handlers';
const server = setupServer(...handlers);
export default server;



// src/mocks/handlers.ts
import { rest } from 'msw';
import fixtures from '../../fixtures';

const handlers = [
  rest.get('http://localhost:3000/restaurants', (req, res, ctx) => {
    const { products } = fixtures;
    return res(
      ctx.status(200), // 안써도 기본이 200
      ctx.json({ products }),
    );
  }),
];
export default handlers;


// App.test.ts
import { render, screen, waitFor } from '@testing-library/react';
import App from './App';

// jest.mock 불필요.

test('App', () => {
  render(<App />);

 //데이터 들어오기전 테스트하면 통과 안되니, waitFor를 걸어준다.
  waitFor(() => {
    screen.getByText('메가반점ㄹㅇㄴ');
    // 안에 있는 이게 될때까지 기다린다. 
    // 기다린다기보다 될때까지 확인한다라는 개념이라함.
  });
});
```

이렇게 하면 안돼야 되는데 돼버린다.

waitFor가 promise를 반환하기 때문에 async await을 걸어줘야하는데,.

```js
이렇게 바꿔주면 통과 안된다.

test('App', async () => {
  render(<App />);

  await waitFor(() => {
    screen.getByText('메가반점');
    // 안에 있는 이게 될때까지 기다린다. 될때까지 확인한다라는 개념이라함.
  });
});
```

이렇게 바꾸고 '메가반점' 값을 줘도 해결되지 않는다.

!! ReferenceError: fetch is not defined !!

## fetch가 브라우저에선 되는데 node에선 안된다

github에서 만든 fetch `polyfill` 해줘야한다.

fetch는 window에 있다.

설치해준다.

```js
npm install whatwg-fetch --save
```

### Polyfill이란

이전 브라우저에서 기본적으로 지원하지 않는 최신 기능을 제공하는 데 필요한 코드.
