# Reusing Logic with Custom Hooks

네트워크에 크게 의존하는 앱.

네트워크 온라인 여부를 추적, 온오프라인 이벤트를 구독하고 해당 상태를 업데이트하는 이펙트를 이용한

아래와 같은 예시를 볼 수 있다.

```js
import { useState, useEffect } from 'react';

export default function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }

    function handleOffline() {
      setIsOnline(false);
    }
    
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```

그리고 온라인 상태인지 추적해 버튼의 text가 바뀌는 버튼도 있다.

```js
import { useState, useEffect } from 'react';

export default function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

위의 상태표시 컴포넌트, 버튼 컴포넌트는 서로 보이는 모양은 다르지만 동일한 로직이 반복되는 것이다.

이런 경우

`useOnlineStatus` 훅이 있으면 두 컴포넌트를 단순화 할 수 있다.

```js
function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}
```

isOnline state와 useEffect까지 옮겨서 커스텀 훅을 만들 수 있다.

```js
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  return isOnline;
}

```

## hook 명명규칙

custom hook은 use로 시작해야한다. (useOnlineStatus, useUserState, use어쩌고)

lint 설정이 react로 되어있으면 안에서 다른 훅을 사용하지 못하도록 막아주기까지 한다고 함.

## 커스텀 훅을 사용하면 상태 자체가 아닌 상태 저장 로직을 공유할 수 있습니다

앞의 예제에서는 네트워크를 켜고 끌 때 두 컴포넌트가 함께 업데이트 되었습니다.

그러나 단일 `isOnline` 상태 변수가 두 컴포넌트 간에 공유된다고 생각하는 것은 잘못된 생각입니다.

```js
function StatusBar() {
  const isOnline = useOnlineStatus();
  // ...
}

function SaveButton() {
  const isOnline = useOnlineStatus();
  // ...
}


--------------------------------------

function StatusBar() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

function SaveButton() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    // ...
  }, []);
  // ...
}

완전 똑같다.
```

이 두 가지 상태 변수와 이펙트는 `완전히 독립적인 상태 변수`입니다.

네트워크가 켜져 있는지 여부에 관계없이 동일한 외부 값으로 동기화했기 때문에 동시에 동일한 값을 갖게 된 것뿐입니다.

```js
import { useState } from 'react';

export default function Form() {
  const [firstName, setFirstName] = useState('Mary');
  const [lastName, setLastName] = useState('Poppins');

  function handleFirstNameChange(e) {
    setFirstName(e.target.value);
  }

  function handleLastNameChange(e) {
    setLastName(e.target.value);
  }

  return (
    <>
      <label>
        First name:
        <input value={firstName} onChange={handleFirstNameChange} />
      </label>

      <label>
        Last name:
        <input value={lastName} onChange={handleLastNameChange} />
      </label>

      <p><b>Good morning, {firstName} {lastName}.</b></p>
    </>
  );
}

```

```js
import { useState } from 'react';

export function useFormInput(initialValue) {
  const [value, setValue] = useState(initialValue);

  function handleChange(e) {
    setValue(e.target.value);
  }

  const inputProps = {
    value: value,
    onChange: handleChange
  };

  return inputProps;
}
```

값이라는 상태 변수를 하나만 선언했다.

이 훅을 Form 컴포넌트에서 두 번 호출해보자.

```js
function Form() {
  const firstNameProps = useFormInput('Mary');
  const lastNameProps = useFormInput('Poppins');
  // ...
```

같은 훅을 두 번 호출하면 두개의 state를 선언한 것 처럼 작동한다.

훅으로 생성한 인스턴스로 생각하니 이해가 쉽다. 각각의 state를 갖는.

**!! 핵심 : 상태 로직을 공유하는 거지 상태 자체의 공유가 아니라는 점 !!**

상태 자체를 공유하고 싶으면 부모로 올려서 공유하라함.

```js
import { useState, useEffect } from 'react';
import { createConnection } from './chat.js';
import { showNotification } from './notifications.js';

export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, serverUrl]);

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}

```

```js
export function useChatRoom({ serverUrl, roomId }) {
  useEffect(() => {
    const options = {
      serverUrl: serverUrl,
      roomId: roomId
    };
    const connection = createConnection(options);
    connection.connect();
    connection.on('message', (msg) => {
      showNotification('New message: ' + msg);
    });
    return () => connection.disconnect();
  }, [roomId, serverUrl]);
}
```

```js
export default function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useChatRoom({
    roomId: roomId,
    serverUrl: serverUrl
  });

  return (
    <>
      <label>
        Server URL:
        <input value={serverUrl} onChange={e => setServerUrl(e.target.value)} />
      </label>
      <h1>Welcome to the {roomId} room!</h1>
    </>
  );
}
```

이렇게 하면 ChatRoom 컴포넌트가 내부에서 어떻게 작동하는지에 대한 걱정 없이

사용자 지정 Hook을 호출할 수 있다.

로직이 여전히 props와 state 변경에 반응하면서 코드는 훨씬 깔끔해진다.

<br>

## when to use custom hooks

커스텀 Hook을 사용해야 하는 경우

중복되는 모든 코드에 대해 사용자 정의 Hook을 추출할 필요는 없다.

하지만 Effect를 작성할 때마다 커스텀 Hook으로 감싸는 것이 더 명확할지 고려하라고 함.

```js
function ShippingForm({ country }) {
  const [cities, setCities] = useState(null);
  // This Effect fetches cities for a country
  useEffect(() => {
    let ignore = false;
    fetch(`/api/cities?country=${country}`)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setCities(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [country]);

  const [city, setCity] = useState(null);
  const [areas, setAreas] = useState(null);
  // This Effect fetches areas for the selected city
  useEffect(() => {
    if (city) {
      let ignore = false;
      fetch(`/api/areas?city=${city}`)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setAreas(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [city]);

  // ...
```

이런 코드는 상당히 반복적이다. 이런 효과는 서로 분리해 유지하는것이 맞다.

서로 다른 두 가지를 동기화하므로 하나의 effect로 병합하면 안된다.

이러한 훅으로 뺀다.

```js
function useData(url) {
  const [data, setData] = useState(null);

  useEffect(() => {
    if (url) {
      let ignore = false;
      fetch(url)
        .then(response => response.json())
        .then(json => {
          if (!ignore) {
            setData(json);
          }
        });
      return () => {
        ignore = true;
      };
    }
  }, [url]);
  return data;
}
```

```js
function ShippingForm({ country }) {
  const cities = useData(`/api/cities?country=${country}`);
  const [city, setCity] = useState(null);
  const areas = useData(city ? `/api/areas?city=${city}` : null);
  // ...
```

custom hook을 추출하면 데이터 흐름을 명시적으로 만들 수 있다.

그리고  Effect를 안으로 숨기게 되어 불필요한 종속성을 추가하는것을 방지할 수 있다.

이상적으로는 시간이 지나면 앱의 effect 대부분이 커스텀훅에 포함될것이라함.

<br>

## Custom Hooks help you migrate to better patterns

`effect는 "탈출구"입니다. "React를 벗어나야 할 때", 그리고 사용 사례에 더 나은 내장 솔루션이 없을 때 사용합니다.`

시간이 지남에 따라 React 팀의 목표는 더 구체적인 문제에 대한 더 구체적인 솔루션을 제공함으로써 앱의 Effect 수를 최소한으로 줄이는 것입니다.

effect를 커스텀 Hook으로 감싸면 이러한 솔루션이 제공될 때 코드를 더 쉽게 업그레이드할 수 있습니다. 


```js
import { useOnlineStatus } from './useOnlineStatus.js';

function StatusBar() {
  const isOnline = useOnlineStatus();
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}

function SaveButton() {
  const isOnline = useOnlineStatus();

  function handleSaveClick() {
    console.log('✅ Progress saved');
  }

  return (
    <button disabled={!isOnline} onClick={handleSaveClick}>
      {isOnline ? 'Save progress' : 'Reconnecting...'}
    </button>
  );
}

export default function App() {
  return (
    <>
      <SaveButton />
      <StatusBar />
    </>
  );
}

```

```js
import { useState, useEffect } from 'react';

export function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);

  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```

위 코드에는 엣지 케이스가 있다.

컴포넌트가 마운트 될 때, isOnline이 기본적으로 true지만

네트워크가 이미 오프라인 상태라면 틀릴 수 있다.

브라우저 navigator.onLine API를 사용하여 이를 확인할 수 있지만

서버에서 React 앱을 실행하여 초기 html을 생성하는 경우,

이를 직접 사용하면 중단될 수 있다. 이를 개선하기 위해

React 18에는 `useSyncExternalStore라는` 전용 API가 포함되어 있다.

```js
import { useSyncExternalStore } from 'react';

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

```

커스텀 Hook으로 effect를 래핑하는 것이 종종 유익한 또 다른 이유들.

* 이펙트를 오가는 데이터 흐름을 매우 명확하게 만들 수 있다.

* 컴포넌트가 Effect의 정확한 구현보다는 의도에 집중할 수 있다.

* React에 새로운 기능이 추가되면 컴포넌트를 변경하지 않고도 해당 효과를 제거할 수 있다.
