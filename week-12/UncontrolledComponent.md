# Uncontrolled component

폼 컴포넌트에서 기본값(default value)이나 초기값(initial value)을 설정하지 않은 상태에서 사용되는 컴포넌트를 말한다.

내부적으로 ref를 사용해 DOM 자체에서 폼 데이터가 다루어 지며, 그렇기 때문에 모든 state의 업데이트에 대해 리렌더링 되지 않아 성능상의 이점이 있다.

그럼 `Uncontrolled component`가 더 좋은거 아니야? -> 일부 상황에서 유용하지만, 모든 상황에서 사용하기에는 적합하지 않을 수 있다.

## 1. 외부에서의 제어

`Uncontrolled component`의 경우, 일부 컴포넌트의 상태를 외부에서 제어하기 어렵기 때문에, 컴포넌트와 관련된 로직이 더 복잡해질 수 있다.

```js
import React, { useRef } from "react";

function UncontrolledInput() {
  const inputRef = useRef();

  const handleSubmit = (event) => {
    event.preventDefault();
    const inputValue = inputRef.current.value;
    // 입력된 데이터 처리 로직
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Input:
        <input type="text" ref={inputRef} />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default UncontrolledInput;
```

```js
import React from "react";

function ControlledInput({ value, onChange, onSubmit }) {
  const handleSubmit = (event) => {
    event.preventDefault();
    onSubmit();
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Input:
        <input type="text" value={value} onChange={onChange} />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default ControlledInput;
```

`Controlled component` 컴포넌트에서는 `value`와 `onChange` prop을 통해 외부에서 값을 제어한다.

외부에서 이 컴포넌트에 입력된 값을 가져오거나 값을 설정하는 것이 가능하며, 다른 컴포넌트에서 이 컴포넌트를 제어하고 상태를 변경할 수 있다는 것.

`Controlled component`를 사용하면 컴포넌트와 관련된 로직이 복잡해지더라도, 외부에서 값을 제어하는 것이 가능해져서 컴포넌트를 보다 더 유연하게 사용할 수 있다.

## 2. 유효성 검사

`Controlled component`의 경우, 컴포넌트의 상태를 관리하고 이를 사용해 유효성 검사를 수행할 수 있다. 이렇게 하면 사용자가 입력한 값이 유효한지를 실시간으로 검사할 수 있고, 필요한 경우에 즉시 에러를 `표시`할 수 있다.

반면 `Uncontrolled component`의 경우, DOM에서 직접 값을 가져오기 때문에 유효성 검사 결과를 표시하기 어렵다. 폼을 제출하기위한 이벤트 함수에서 검사가 진행되기 때문에 실시간 검사가 어렵다.

따라서, `Controlled component`를 사용하면 보다 직관적이고 실시간으로 유효성 검사를 수행할 수 있는 것.

```js
// 상태를 이용해 바로 바로 리렌더링되며, 유효성 검사를 한다.
import React, { useState } from "react";

function ControlledForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [nameError, setNameError] = useState("");
  const [emailError, setEmailError] = useState("");

  const handleSubmit = (event) => {
    event.preventDefault();
    if (!name) {
      setNameError("Name is required");
      return;
    }
    if (!email) {
      setEmailError("Email is required");
      return;
    }
    // 입력된 데이터 처리 로직
  };

  const handleNameChange = (event) => {
    setName(event.target.value);
    setNameError("");
  };

  const handleEmailChange = (event) => {
    setEmail(event.target.value);
    setEmailError("");
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Name:
        <input type="text" value={name} onChange={handleNameChange} />
        {nameError && <span>{nameError}</span>}
      </label>
      <label>
        Email:
        <input type="email" value={email} onChange={handleEmailChange} />
        {emailError && <span>{emailError}</span>}
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default ControlledForm;
```

```js
import React, { useRef } from "react";

function UncontrolledForm() {
  const nameInputRef = useRef();
  const emailInputRef = useRef();
  const nameErrorRef = useRef();
  const emailErrorRef = useRef();

  const handleSubmit = (event) => {
    event.preventDefault();
    const nameValue = nameInputRef.current.value;
    const emailValue = emailInputRef.current.value;
    if (!nameValue) {
      nameErrorRef.current.innerText = "Name is required";
      return;
    }
    if (!emailValue) {
      emailErrorRef.current.innerText = "Email is required";
      return;
    }
    // 입력된 데이터 처리 로직
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Name:
        <input type="text" ref={nameInputRef} />
        <span ref={nameErrorRef}></span>
      </label>
      <label>
        Email:
        <input type="email" ref={emailInputRef} />
        <span ref={emailErrorRef}></span>
      </label>
      <button type="submit">Submit</button>
    </form>
  );
}

export default UncontrolledForm;
```

3. `Controlled component`는 React의 장점 중 하나인 단방향 데이터 흐름을 활용할 수 있다. 상태가 변경될 때마다 자동으로 컴포넌트를 다시 렌더링하여 변경된 상태를 반영하는점을 활용하여 컴포넌트의 상태를 제어하면, 애플리케이션의 상태를 더욱 예측 가능하게 만들 수 있다.

```js
import React, { useState } from "react";

function ControlledCounter() {
  const [count, setCount] = useState(0);

  const handleIncrement = () => {
    setCount(count + 1);
  };

  const handleDecrement = () => {
    setCount(count - 1);
  };

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={handleIncrement}>+</button>
      <button onClick={handleDecrement}>-</button>
    </div>
  );
}

export default ControlledCounter;
```

```js
import React, { useRef } from "react";

function UncontrolledCounter() {
  const countRef = useRef(0);

  const handleIncrement = () => {
    countRef.current += 1;
    console.log("Count:", countRef.current);
  };

  const handleDecrement = () => {
    countRef.current -= 1;
    console.log("Count:", countRef.current);
  };

  return (
    <div>
      <p>Count: {countRef.current}</p>
      <button onClick={handleIncrement}>+</button>
      <button onClick={handleDecrement}>-</button>
    </div>
  );
}

export default UncontrolledCounter;
```

`countRef는` 컴포넌트 내부에서 상태를 관리하지 않고, 값을 직접 업데이트한다.

`handleIncrement와` `handleDecrement` 함수에서는 `countRef.current`를 직접 수정하여 값을 업데이트한다.

값을 직접 변경하지만 변경에 따른 렌더링 처리가 자동으로 이뤄지지 않는다. 상태가 예측 가능하지 않다고 할 수 있다. 수동으로 처리해 줄 수는 있다. -> 복잡한데 굳이;

따라서, `Controlled component`와 `Uncontrolled component`는 상황에 따라 다르게 선택되어야 한다.

`Controlled component`는 입력 값의 검증이나 다른 컴포넌트와의 상호작용이 필요한 경우에 적합하며, `Uncontrolled component`는 단순한 폼의 경우나, 성능 개선이 필요한 경우에 적합하다.
