# 1. external store

[실습레포](https://github.com/heyho00/external-store/tree/external-store)

## 관심사의 분리

하나의 시스템은 작은 부품이 모여서 만들어진다.

우리는 이미 작은 컴포넌트를 합쳐서 더 큰 컴포넌트를 만드는 방식으로 개발하고 있다.

Layered Architecture 에서는 사용자에게 `가까운 것`과 `먼 것`으로 구분한다.

가장 가까운 건 UI를 다루는 부분, 그 다음엔 Business Logic을 다루는 부분,

그 너머에는 데이터에 접근하고 저장하는 부분으로 나눌 수 있게 된다.

`Input → Process → Output`

널리 알려진 MVC로 거칠게 표현하자면 이렇다. 각 부분은 하나의 관심사로 격리됨으로써 복잡도를 낮추게 된다.

- Model → Process
- View → Output
- Controller → Input

<br>

## Flux Architecture

acebook(현 Meta)에서 `MVC의 대안`으로 내세운 아키텍처.

2-way binding을 썼을 때 생길 수 있는 Model-View의 복잡한 관계(전통적인 MVC에선 이런 상황을 지양한다)를 겨냥해 명확히 “unidirectional data flow”를 강조한다.

단방향 데이터 흐름.

1. Action → 이벤트/메시지 같은 객체.

2. Dispatcher → (여러) Store로 Action을 전달. 메시지 브로커와 유사하다.

3. Store (여러 개) → 받은 Action에 따라 상태를 변경. 상태 변경을 알림.

4. View → Store의 상태를 반영.

Redux는 단일 Store를 사용함으로써 좀 더 단순한 그림을 제안한다.

1. Action

2. Store → dispatch를 통해 Action을 받고, 사용자가 정의한 reducer를 통해 State를 변경한다.

3. View → State를 반영.

상태를 UI에 반영하는 방법은 모두 동일하다.

3단계 프로세스로 거칠게 매핑하면 다음과 같다.

- Input → Action + dispatch

- Process → reducer

- Output → View(React)

## External Store

store가 리액트 안에 있지 않다.
아키텍처적인 관점이 아닌 리액트 관점에서.

const [count, setCount] = useState(1) 이런식으로 직접관리 안한다는 것.

class 컴포넌트 시절에는 forceUpdate를 사용해 state아닌 값의 변화에

리렌더를 강제로 해줬었다.

이제는 useReducer를 쓸 수 있고, setState가 내부적으로 useReducer를 사용해 리렌더 하는 것.

```js
const [, setState] = useState({});
const forceUpdate = () => setState({});
```

커스텀 hook으로 만든다.

```js
function useForceUpdate() {
  const [, setState] = useState({});
  return useCallback(() => setState({}), []);
}
```

이런 접근을 잘 하면, React가 UI를 담당하고, 순수한 TypeScript(또는 JavaScript)가 비즈니스 로직을 담당하는, `관심사의 분리(Separation of Concerns)`를 명확히 할 수 있다.

자주 바뀌는 UI 요소에 대한 테스트 대신, 오래 유지되는(바뀌면 치명적인) `비즈니스 로직에 대한 테스트 코드`를 작성해 유지보수에 도움이 되는 테스트 코드를 치밀하게 작성할 수 있다.
