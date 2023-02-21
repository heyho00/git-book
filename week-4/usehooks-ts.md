# 5. usehooks-ts

## 학습 키워드

* usehooks-ts
  * useBoolean
  * useEffectOnce
  * useFetch
  * useInterval
  * useEventListener
  * useLocalStorage
  * useDarkMode
* swr
* react-query

[실습 레포 - usehooks-ts 브랜치](https://github.com/heyho00/timer-app/tree/usehook-ts)

[모든 hook에 대한 코드들](https://usehooks-ts.com/)

## 설치

```bash
npm i usehooks-ts
```

## 사용

여러 훅들이 만들어져 있다. 걍 호출해서 쓰면됨.

```js
import { useEffect, useState } from "react";
import { useBoolean } from "usehooks-ts";

export default function TimerControl() {
    const {value:playing, toggle} = useBoolean()
    const {count, increment} = useCounter(0)

    return (
        <div>
            {playing ?(
                <Timer />
            ): (
                <p>Stop</p>
            ) }
            <button type="button" onClick={toggle}>
                Toggle
            </button>
            <hr />
            <p>{count}</p>
            <button type="button" onClick={increment}>
                increase
            </button>
            
        </div>
    )
}
```

useEffectOnce 라는 훅도 불러 쓸 수 있는데,

의존성 배열만 뺀 것이고 한번만 실행시키는 훅이다.

## 몇 가지 기능이 살짝 더 있는 라이브러리도 있다

* use-http

## 좀 더 복잡하지만 캐시 이슈를 고려한 좋은 대안

[swr](https://swr.vercel.app/ko)

[react-query](https://tanstack.com/query/latest)

## useInterval

setInterval 관련해 리액트에서 코드를 짜다보면 상태와 맞물려 문제가 될 확률이 다분해서 useInterval을 쓰는게 좋다고 함.

## useEventListener

아무데나 클릭했을때의 listener라던지,

로그인을 하는데 외부와 많이 물려있는 코드가 있을때

훅으로 빼거나 아예 밖으로 빼고싶을때 이걸 쓰면 편하다고 함. 케이스 스터디 필요.

## useLocalStorage

객체를 넣는 경우가 많다.

다크모드 세팅에 유용.

로컬에 넣는 이유는 새로고침하거나 다른 이벤트가 있어도 유지하고 싶기 때문.

전역적으로 다른데서 받아 쓸 수 있는 개념.

.
.
.
이 외에도 많다.
