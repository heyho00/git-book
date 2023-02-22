# Custom Hooks - Challenges

## 공식문서에 있는 challenges

제공하는 sandbox에서 실습 후 기록한다.

### 1. useCounter

```js
// App.js
import { useCounter } from "./useCounter";

export default function Counter() {
  const count = useCounter();
  return <h1>Seconds passed: {count}</h1>;
}

// useCounter.js
import { useState, useEffect } from "react";

export function useCounter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return count;
}

```

### 2. Make the counter delay configurable

슬라이더로 조절하는 지연 값을 커스텀 훅에 전달하고, 1000ms를 하드코딩하는 대신 전달된 지연을 사용하도록 카운터 훅을 변경해보자.

```js
import { useState } from 'react';
import { useCounter } from './useCounter.js';

export default function Counter() {
  const [delay, setDelay] = useState(1000);
  const count = useCounter(delay);

  return (
    <>
      <label>
        Tick duration: {delay} ms
        <br />
        <input
          type="range"
          value={delay}
          min="10"
          max="2000"
          onChange={e => setDelay(Number(e.target.value))}
        />
      </label>
      <hr />
      <h1>Ticks: {count}</h1>
    </>
  );
}

```

```js
import { useState, useEffect } from "react";

export function useCounter(delay) {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, delay);

    return () => clearInterval(id);
  }, [delay]);

  return count;
}

```

### 3. Extract useInterval out of useCounter

현재 사용 중인 카운터 훅은 두 가지 작업을 수행한다.

간격을 설정하고 간격이 틱될 때마다 상태 변수를 증가시킨다.

간격을 설정하는 로직을 useInterval이라는 별도의 Hook으로 분리하세요. 이 Hook은 두 개의 인수를 받아야 합니다:

onTick callback 및 delay.

```js
// App.js
import { useCounter } from "./useCounter.js";

export default function Counter() {
  const count = useCounter(1000);
  return <h1>Seconds passed: {count}</h1>;
}

//useCounter.js
import { useState } from "react";
import { useInterval } from "./useInterval.js";

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount((c) => c + 1);
  }, delay);
  return count;
}

// useInterval.js
import { useEffect } from "react";

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => clearInterval(id);
  }, [onTick, delay]);
}

```

원래는 useCounter에서 useEffect로 처리했는데

setInterval을 따로 빼려고 하면 useEffect 째로 빼줘야 함.

훅 규칙에 의해 이런 구조에서는 useEffect가 맨 하위로 가야한다.

### 4. Fix a resetting interval

이 예에서는 두 개의 개별 간격이 있습니다.

App 컴포넌트는 useCounter를 호출하고, 이 컴포넌트는 useInterval을 호출하여 매초마다 카운터를 업데이트합니다.

그러나 App 컴포넌트는 또한 useInterval을 호출하여 2초마다 페이지 배경색을 임의로 업데이트합니다.

어떤 이유에서인지 페이지 배경을 업데이트하는 콜백이 실행되지 않습니다. 사용 간격 안에 몇 가지 로그를 추가합니다:

```js
useEffect(() => {
    console.log('✅ Setting up an interval with delay ', delay)
    const id = setInterval(onTick, delay);
    return () => {
      console.log('❌ Clearing an interval with delay ', delay)
      clearInterval(id);
    };
  }, [onTick, delay]);
```

로그가 예상한 것과 일치하나요? 일부 이펙트가 불필요하게 재동기화되는 것 같다면 어떤 종속성 때문에 그런 일이 발생하는지 짐작할 수 있나요? 이펙트에서 해당 종속성을 제거할 수 있는 방법이 있나요?

문제를 해결한 후에는 페이지 배경이 2초마다 업데이트될 것으로 예상됩니다.

힌트 : 사용중인 Interval Hook이 이벤트 리스너를 인수로 받아들이는 것 같습니다.

이벤트 리스너가 Effect의 종속성이 될 필요가 없도록 이벤트 리스너를 감싸는 방법을 생각해낼 수 있을까요?

<br>

useInterval 내에서 틱 콜백을 effect event에 래핑합니다.

이렇게 하면 Effect의 종속성에서 onTick을 생략할 수 있습니다.

컴포넌트를 다시 렌더링할 때마다 Effect가 다시 동기화되지 않으므로

페이지 배경색 변경 간격이 매초마다 재설정되지 않습니다.

이 변경으로 두 간격이 모두 예상대로 작동하며 서로 간섭하지 않습니다:

```js
// App.js
import { useCounter } from './useCounter.js';
import { useInterval } from './useInterval.js';

export default function Counter() {
  const count = useCounter(1000);

  useInterval(() => {
    const randomColor = `hsla(${Math.random() * 360}, 100%, 50%, 0.2)`;
    document.body.style.backgroundColor = randomColor;
  }, 2000);

  return <h1>Seconds passed: {count}</h1>;
}



// useCounter.js
import { useState } from 'react';
import { useInterval } from './useInterval.js';

export function useCounter(delay) {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(c => c + 1);
  }, delay);
  return count;
}



// 바꾸기 전 useInterval.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(onTick, delay) {
  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => {
      clearInterval(id);
    };
  }, [onTick, delay]);
}


// useInterval.js
import { useEffect } from 'react';
import { experimental_useEffectEvent as useEffectEvent } from 'react';

export function useInterval(callback, delay) {
  const onTick = useEffectEvent(callback)

  useEffect(() => {
    const id = setInterval(onTick, delay);
    return () => {
      clearInterval(id);
    };
  },[delay]);
}

```

callback을 useEffectEvent를 이용해  의존성 배열에서 빼줬는데,

useEffectEvent 사용하지않고 그냥 의존성 배열에서 onTick 빼기만해도 작동한다.

useEffectEvent는 아직 stable version에서는 사용할 수 없다.

비반응 로직을 이펙트 이벤트로 추출할 수 있는 hook이라는 설명이 있는데,

그냥 의존성 배열에서 뺀것과 정확히 어떻게 다른지는 아직 모르겠다.

아무튼 위 코드에서는 의존성 배열에 리렌더 될 조건이 아닌 콜백함수까지 포함되어 있었다는게 핵심으로 보인다.

<br>

### 5. Implement a staggering movement

이 예시에서 `usePointerPosition` Hook은 현재 포인터 위치를 추적합니다.

커서나 손가락을 영역 위로 이동하면 빨간색 점이 움직임을 따라가는 것을 확인할 수 있습니다.

그 위치는 pos1 변수에 저장됩니다.

실제로는 다섯 개(!)의 다른 빨간색 점이 렌더링되고 있습니다.

현재는 모두 같은 위치에 나타나기 때문에 보이지 않습니다. 이 부분을 수정해야 합니다.

대신 구현하려는 것은 "엇갈린" 움직임입니다.

각 점이 이전 점의 경로를 "따라야" 합니다.

예를 들어 커서를 빠르게 이동하면 첫 번째 점은 즉시 따라가고, 두 번째 점은 약간의 지연을 두고 첫 번째 점을 따라가고,

세 번째 점은 두 번째 점을 따라가는 식으로 시차를 두어야 합니다.

사용 지연된 값 사용자 정의 Hook을 구현해야 합니다.

현재 구현은 제공된 값을 반환합니다. 대신 밀리초 전 지연에서 값을 다시 반환하고 싶습니다.

이를 위해서는 state와 Effect가 필요할 수 있습니다.

사용 지연된 값을 구현하고 나면 점들이 서로 따라 움직이는 것을 볼 수 있을 것입니다.

```js
// 기존 App.js
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  // TODO: Implement this Hook
  return value;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}


// usePointerPosition
import { useState, useEffect } from 'react';

export function usePointerPosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    function handleMove(e) {
      setPosition({ x: e.clientX, y: e.clientY });
    }
    window.addEventListener('pointermove', handleMove);
    return () => window.removeEventListener('pointermove', handleMove);
  }, []);
  return position;
}

```

```js
// App.js
import { useState, useEffect } from 'react';
import { usePointerPosition } from './usePointerPosition.js';

function useDelayedValue(value, delay) {
  const [delayedValue, setDelayedValue] = useState(value);

  useEffect(() => {
    setTimeout(() => {
      setDelayedValue(value);
    }, delay);
  }, [value, delay]);

  return delayedValue;
}

export default function Canvas() {
  const pos1 = usePointerPosition();
  const pos2 = useDelayedValue(pos1, 100);
  const pos3 = useDelayedValue(pos2, 200);
  const pos4 = useDelayedValue(pos3, 100);
  const pos5 = useDelayedValue(pos3, 50);
  return (
    <>
      <Dot position={pos1} opacity={1} />
      <Dot position={pos2} opacity={0.8} />
      <Dot position={pos3} opacity={0.6} />
      <Dot position={pos4} opacity={0.4} />
      <Dot position={pos5} opacity={0.2} />
    </>
  );
}

function Dot({ position, opacity }) {
  return (
    <div style={{
      position: 'absolute',
      backgroundColor: 'pink',
      borderRadius: '50%',
      opacity,
      transform: `translate(${position.x}px, ${position.y}px)`,
      pointerEvents: 'none',
      left: -20,
      top: -20,
      width: 40,
      height: 40,
    }} />
  );
}

```

연쇄적으로 작동..

이 효과는 cleanup이 필요 없다.

cleanup에서 `clearTimeout`을 호출하면 값이 이미 예약된 타임아웃이 재설정 된다.

동작을 계속 유지하려면 모든 타임아웃이 실행되도록 해야 한다.
