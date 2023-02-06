# JSX

XML 같은 `문법 확장`, `JS를 확장한 문법`.

React.createElement을 쓰는 JavaScript 코드로 변환된다.

중괄호를 써서 JavaScript 코드를 그대로 쓸 수 있고, 결국은 JavaScript 코드와 1:1로 매칭된다.

babel을 이용해 어떻게 변환되는지 확인할 수 있다.

```js
→ “Presets”에서 “react”를 체크하거나, “Plugins”에서 “@babel/plugin-transform-react-jsx”를 추가하면 JSX를 실험할 수 있다.

JSX 파일에 /* @jsx 어쩌고 */ 주석을 추가하면 React.createElement 대신 “어쩌고”를 쓰게 된다.
```

```js
<p>Hello, world!</p>
// 변환
React.createElement("p", null, "Hello, world!");


<Greeting name="world" />
// 변환
React.createElement(Greeting, { name: "world" });


<Button type="submit">Send</Button>
// 변환
React.createElement(Button, { type: "submit" }, "Send");


<div className="test">
 <p>Hello, world!</p>
 <Button type="submit">Send</Button>
</div>
//변환
React.createElement(
 "div",
 { className: "test" },
 React.createElement("p", null, "Hello, world!"),
 React.createElement(Button, { type: "submit" }, "Send")
);


<div>
 <p>Count: {count}!</p>
 <button type="button" onClick={() => setCount(count + 1)}>Increase</button>
</div>
// 변환
React.createElement(
 "div",
 null,
 React.createElement("p", null, "Count: ", count, "!"),
 React.createElement("button", { type: "button", onClick: () => setCount(count + 1) }, "Increase")
);

```

## React Element

JSX 쓰지않고 React.createElement를 써서 React Element 트리를 갱신하는데 쓸 수 있다. 필수 아니다.

```js
class Hello extends React.Component {
  render() {
    return <div>Hello {this.props.toWhat}</div>;
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Hello toWhat="World" />);

이거를 아래처럼 써도 된다.

class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(React.createElement(Hello, {toWhat: 'World'}, null));

컴파일 설정을 하지 않아도 된다. 변환할 필요가 없으니.
JSX로 할 수 있는 것은 모두 순수 JS로도 할 수 있다.

```

JSX Runtime은 _jsx란 함수를, Preact는 h란 함수를 직접 지원.

내부적으로 createElement 대신 리액트 17부터 저걸 이용해 돔을 그린다는거.

## VDOM (Virtual DOM)

Virtual DOM (VDOM)은 UI의 이상적인 또는 “가상”적인 표현을 메모리에 저장하고 ReactDOM과 같은 라이브러리에 의해 “실제” DOM과 `동기화`하는 프로그래밍 개념.

이 과정을 `재조정`이라고 합니다.

## 재조정

노드를 만들때 어딘 바꼈다, 안바꼈다 이런거 안해준다. 무조건 아래 코드같은거 재실행.

트리를 바꿔주는거고 그것과 화면을 계속 비교.

사실은 일을 두 번 하는것.

```JS
class Hello extends React.Component {
  render() {
    return React.createElement('div', null, `Hello ${this.props.toWhat}`);
  }
}
```

`react developer tool`을 이용해서 컴포넌트 구조를 확인할 수 있다.

정확히 VDOM을 보여주는건 아니다.

상태마다 리렌더되는 컴포넌트도 확인할 수 있다. 디버깅에 유용.

## VDOM을 쓰는 이유

무작정 VDOM을 쓰는게 빠르다는 의견은 맞지 않다. (svelte왈 "vdom왜써? 없어도 좋은뎅?")

유지보수에 유리하기 때문. `선언형 UI`로 더 나은 구조를 제공하기 때문에 쓴다.

중요한 것은, VDOM이 무엇이고 왜 쓰는지 안다면 활용할 수 있는 `최적화 기법`이 있다는 것.
