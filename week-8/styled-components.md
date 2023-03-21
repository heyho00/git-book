# styled-components

## 컴포넌트로 생각하기

JSS는 CSS보다 더 강력한 추상화이다.

JS를 사용해 스타일을 선언적이고 유지보수 가능한 방식으로 설명한다.

### 인라인 스타일이 동작하는 방법

```js
const textStyles = {
  color: white,
  backgroundColor: black
}

<p style={textStyles}>inline style!</p>
```

브라우저에서 DOM노드를 다음과 같이 연결한다.

```js
<p style="color: white; backgrond-color: black;">inline style!</p>
```

## CSS in JS가 동작하는 방법

```js
import styled from 'styled-components';

const Text = styled.div`
  color: white,
  background: black
`

<Text>Hello CSS-in-JS</Text>
```

브라우저에서 DOM노드를 다음과 같이 연결한다.

```js
<style>
.hash136s21 {
  background-color: black;
  color: white;
}
</style>

<p class="hash136s21">Hello CSS-in-JS</p>
```

## 차이점

CSS in JS는 DOM의 상단에 `<style>` 태그를 추가했고,

인라인 스타일은 DOM 노드에 속성으로 추가했습니다.

왜 이 문제가 중요할까?

```
모든 CSS 기능을 JavaScript 이벤트 핸들러로 지정할 수 있는 것은 아닙니다. 많은 슈도 선택자(:disabled, :before, :nth-child)도 불가능하고, html, body태그 등도 지원하지 않습니다.
```

CSS in JS를 사용하면 손쉽게 모든 CSS의 힘을 그대로 누릴 수 있다.

실제 CSS가 생성되기 때문에 모든 미디어쿼리, 수도 선택자도 사용 가능.

여러 라이브러리 소개

<https://d0gf00t.tistory.com/22>

## CSS-in-JS와 성능
