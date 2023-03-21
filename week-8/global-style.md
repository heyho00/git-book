# global style

## keyword

- Reset CSS

- `box-sizing` 속성

- `word-break` 속성

- Theme

- ThemeProvider

## Reset CSS

```js
styled-reset 편하다.
```

```js
import { Reset } from "styled-reset";

export default function App() {
  return (
    <>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </>
  );
}
```

간편하게 reset과 global style을 잡아줄 수 있다.

```js
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
 html {
  box-sizing: border-box;
 }
 
 *,
 *::before,
 *::after {
  box-sizing: inherit;
 }
 
 html {
  font-size: 62.5%;
 }
 
 body {
  font-size: 1.6rem;
 }
 
 :lang(ko) {
  h1, h2, h3 {
   word-break: keep-all;
  }
 }
`;

export default GlobalStyle;
```

## Theme

디자인 시스템의 근간을 마련하는데 활용. 잘 정의하면 다크 모드 등에 대응하기 쉬움. 눈에 보이는 단편적인 정보를 넘어서, “의미”에 집중할 수 있게 됨.

일단 기본 Theme부터 정의.

```js
const defaultTheme = {
  colors: {
    background: "#FFF",
    text: "#000",
    primary: "#F00",
    secondary: "#00F",
  },
};

export default defaultTheme;
```

```js
// App.tsx

import { ThemeProvider } from "styled-components";

import { Reset } from "styled-reset";

import defaultTheme from "./styles/defaultTheme";

import GlobalStyle from "./styles/GlobalStyle";

export default function App() {
  return (
    <ThemeProvider theme={defaultTheme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
    </ThemeProvider>
  );
}
```

props.theme을 활용

```js
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
 body {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
 
 a {
  color: ${(props) => props.theme.colors.text};
 }
 
 button,
 input,
 select,
 textarea {
  background: ${(props) => props.theme.colors.background};
  color: ${(props) => props.theme.colors.text};
 }
`;

export default GlobalStyle;
```

타입 문제를 해결하기 위해 styled.d.ts 파일 작성.

```js
import 'styled-components';

declare module 'styled-components' {
 export interface DefaultTheme extends Theme {
  colors: {
   background: string;
   text: string;
   primary: string;
   secondary: string;
  }
 }
}
```

타입을 정의하고 defaultTheme을 맞추는 게 불편하니, 반대로 defaultTheme에서 타입을 추출하자.

```js
import defaultTheme from "./defaultTheme";

type Theme = typeof defaultTheme;

export default Theme;
```

타입 파일 변경

```js
import 'styled-components';

import Theme from './Theme';

declare module 'styled-components' {
 export interface DefaultTheme extends Theme {}
}
```

다른 theme을 추가할 때 Theme 타입을 사용. 항상 defaultTheme에 먼저 항목을 추가/삭제하고, 나머지를 여기에 맞추면 된다.

어떤 프로퍼티가 빠지고 추가됐는지 알 수 있다.

```js
import Theme from "./Theme";

const darkTheme: Theme = {
  colors: {
    background: "#000",
    text: "#FFF",
    primary: "#F00",
    secondary: "#00F",
  },
};

export default darkTheme;
```

의미를 명확히 드러냈다면, 다크 모드를 지원하는 것도 쉽다.

```js
import { useDarkMode } from "usehooks-ts";

import { ThemeProvider } from "styled-components";

import { Reset } from "styled-reset";

import defaultTheme from "./styles/defaultTheme";
import darkTheme from "./styles/darkTheme";

import GlobalStyle from "./styles/GlobalStyle";

import Greeting from "./components/Greeting";
import Button from "./components/Button";

export default function App() {
  const { isDarkMode, toggle } = useDarkMode();

  const theme = isDarkMode ? darkTheme : defaultTheme;

  return (
    <ThemeProvider theme={theme}>
      <Reset />
      <GlobalStyle />
      <Greeting />
      <Button onClick={toggle}>Dark Theme Toggle</Button>
    </ThemeProvider>
  );
}
```

Jest 테스트 쪽에서 “window.matchMedia” 문제 발생.

```js
// src/setupTests.ts
Object.defineProperty(window, "matchMedia", {
  writable: true,
  value: jest.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(), // deprecated
    removeListener: jest.fn(), // deprecated
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```
