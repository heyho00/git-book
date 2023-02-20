# React hook

## 학습 키워드

* React Hook 이란
* Hooks
  * useState
  * useEffect
  * useContext
  * useRef
  * useLayoutEffect
* React StrictMode 란

React 16.8 에서 hook이 도입되기 전까지는,

상태를 가진 컴포넌트는 Class component로 만들고, props만 사용하는 재사용 가능한

작은, 퓨어한 컴포넌트만 functional component로 작성했었다.

현재는 functional만 쓰게됨.

## useEffect

렌더링 이후에 해야할 일. 즉 react의 외부와 관련된 일을 정해줄 수 있다.

기본적으로 렌더링 때마다 실행되며, 의존성 배열을 이용해 언제 이펙트를 실행할지 지정할 수 있다(= 불필요한 경우 리렌더 하지 않음).

함수를 리턴함으로써 종료 처리를 할 수 있다.

### 타이머 예제

[timer app - useEffect 연습레포](https://github.com/heyho00/timer-app)

React의 외부에 우아하게 접근하는 법.

이 정도는 useEffect를 안 쓴다고 크게 문제가 되지 않지만, 이렇게 쓰는 습관을 들이자.

```js
useEffect(() => {
  document.title = `Now: ${new Date().getTime()}`;
});
```

## useEffect 안의 함수를 밖으로 빼도 되나

[함수를 이펙트 안으로 옮기기](https://overreacted.io/ko/a-complete-guide-to-useeffect/#%ED%95%A8%EC%88%98%EB%A5%BC-%EC%9D%B4%ED%8E%99%ED%8A%B8-%EC%95%88%EC%9C%BC%EB%A1%9C-%EC%98%AE%EA%B8%B0%EA%B8%B0)

useEffect 클로저라서 안에서 참조하는 다른 state가 있으면 그걸 처음에 바인딩해서
사용하기 때문에 문제가 될 수 있다.

가능하면 다 안쪽으로 넣으라는 말.

## cleanup과 관련한 사례.

사실 오늘 겪은 따끈따끈한 사례.

사용자 list를 보여주는 modal-ui가 있다. 맨 위에는 사용자 정보를 등록하는 버튼이 있다.

그 밑에 사용자 list는 각 각 큰버튼이며 그 버튼 안에 수정, 삭제 버튼이 있다.

modal을 끄면 아래의 에러가 warning이 발생.

```js
Warning: Can't perform a React state update on an unmounted component.
This is a no-op, but it indicates a memory leak in your application.
To fix, cancel all subscriptions and asynchronous tasks in a useEffect cleanup
```

unmount되는 컴포넌트의 state를 update할 수 없다. 

언마운트된 컴포넌트에서는 상태를 추적할 수 없고, 상태를 추적하지 않기에 작업이 수행되지는 않지만,

메모리 누수가 발생할 수 있으니, useEffect의 cleanup 함수를 이용해라

구글링 해보면 useEffect 안에 cleanup 함수를 이용해 해결한 사례를 많이 볼 수 있다.

내 경우는 useEffect 에서 사용자 데이터를 가져왔고, 사용자를 클릭해 선택할때마다 useEffect가 돌도록 

했기 때문이다. CRUD를 구현하며 내 정보, 내정보는 아니지만 등록한 사람정보, 삭제, 수정, 등록 이벤트가

있을때마다 다시 list가 렌더되도록 해버렸기 때문에 동기화는 빨리 됐지만

사용자를 선택할때마다 세개의 api를 호출하였고 pending 상태일때 모달을 꺼버렸기 때문에 발생했다.

일단 한 개의 api는 앞으로 한번도 변하지 않아도 돼서 따로 useEffect에 빈 의존성 배열로 나누고

다른건 의존성 배열을 정리했다. render라는 state를 만들고 list가 리렌더 되어야할 이벤트에 추가해주는 식으로.

<br>

## fetch 할 때 주의사항

page를 바꿀 경우, 새로 읽어올 경우.

방금 부른걸 취소하지 못하고 둘 다 업데이트 될지 모르니,

그 전꺼를 작동하지 않도록 하는 방법. 종료처리.

위에 내 사례에서는 이런 방식의 cleanup으로 해결되지 않았는데,

useEffect를 연속해서 불러서가 아니라 한번을 하더라도 pending이 끝나기 전에 모달을 닫아서 인 것으로 보인다.

모달이 닫히면서 cleanup 함수에서 ignore를 토글해서 호출을 안하게 되는건데 사실 그럼 그렇게 해도 되야했던거 아닌가.

좀 헷갈리네..

중요한 건, 개발자 도구 network를 보고 통신이 어떻게 돌아가는지 파악하는 것이라는 생각이 들었다.

```js
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);

```
