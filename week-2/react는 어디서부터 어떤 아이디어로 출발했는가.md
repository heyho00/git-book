# react는 어디서부터 어떤 아이디어로 출발했는가

[react 만들어보기 실습 레포](https://github.com/heyho00/make-react)

## 'JSX는 왜 쓰는가'의 답이기도 하다

* html 파일이 있다.
`태그`와 태그안의 속성인 `어트리뷰트`, `밸류`가 문자열로 이루어져있다.<br>이 문자열을 브라우저가 읽어서 `DOM`이라는 형태를 만들고 렌더링 프로세서를 거쳐 화면에 렌더된다.

* 자바스크립트 파일은 `DOM API`를 이용해서 자바스크립트로 `런타임`에 돔을 그린다. html을 생성한다라고도 할 수 있다.

```js
// 예시
  const list = document.querySelector('.list-group')
  const container = document.createElement('li')
  const todoStatus = document.createElement('input')

  container.classList.add('list-group-item', 'list-group-flat')
  container.appendChild(todoStatus)
```

그러나, 이러한 JS 코드에는 문제가 있다.

html은 기본적으로 구조를 갖고있지만
DOM API를 이용해 html파일을 조작하는 JavaScript에는
구조가 없다.

이러한 DOM API는 복잡하고 쓰기 귀찮지만 DOM을 조작하기위해선 사용해야만 한다.

### 💥 DOM API는 버릴 순 없지만 그럼에도 불구하고 DOM을 쓰지 말아보자

"개발자에게 돔을 직접 제어하지 못하게 하자."

"우리가 제공하는 편리한 도구를 주고 돔은 리액트가 조작하게 하자."

다루기 어려운 포맷이 있다면 쉬운 포맷으로 바꿔서 그 쉬운 포맷을 다뤄보자.

추상화된 DOM API 라고도 할 수 있겠다.

무튼, 이런 아이디어다.

<br>

## createDom

```html
<!doctype html>
<html>
<head>
 <meta charset="utf-8">
 <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
 <title>react를 만들어보자</title>
</head>
<body>
  <div id="root"></div>
  <script src="app.js" type="module"></script>
</body>
</html>
```

```js
// app.js

// 개별적인 돔 노드를 다루는 함수기때문에 인자의 이름을 node로 지어준다.
function createDOM(node) {
  // 4. children이 재귀적으로 들어오는데 object 타입이 아닌 string이며 문자열 돔을 만들어 return 해준다.
  if (typeof node === 'string') {
    return document.createTextNode(node);
  }

  const element = document.createElement(node.tag); // 1. vdom 객체를 받아 dom element를 만들었다.

  node.children
    .map(createDOM) //2. 재귀 패턴 하위 children을 순회하며 구조를 만든다.
    .forEach(element.appendChild.bind(element));// 3. element 컨텍스트를 지켜주기위해 bind 해준다. 부모 element의 자식 요소로 추가되게 된다.

  return element;
}

const vdom = {
  tag: 'p',
  props: {},
  children: [
    {
      tag: 'h1',
      props: {},
      children: ["잼는거 list"],
    },
    {
      tag: 'ul',
      props: {},
      children: [
        {
          tag: 'li',
          props: {},
          children: ["피지컬100"]
        },
        {
          tag: 'li',
          props: {},
          children: ["우마게임"]
        },
        {
          tag: 'li',
          props: {},
          children: ["블랙컴뱃"]
        },
      ]
    }
  ],
};

document
  .querySelector('#root')
  .appendChild(createDOM(vdom));  
```
