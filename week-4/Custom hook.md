# Custom hook

[실습 레포 - custom hook 브랜치](https://github.com/heyho00/timer-app/tree/customHook)

로직을 재사용하기 위한 제일 쉬운 방법.

평범하게 `Extract Function`을 수행하면 된다.

컴포넌트가 대문자로 시작하는 PascalCase로 이름을 붙인다면,

Hook은 `use`로 시작하는 camelCase로 이름을 붙이면 된다.

## 이런 코드가 있다고 해보자

```js
import { useEffect, useState } from "react";
import TimerControl from "./components/TimerControl";

export default function App() {
 const [products, setProducts] = useState([]);
 
 useEffect(() => {
  const fetchProducts = async () => {
   const url = 'http://localhost:3000/products';
   const response = await fetch(url);
   const data = await response.json();
   setProducts(data.products);
  };

  fetchProducts();
 }, []);

  return (
  <>
   <TimerControl />
   <p>Hello, world!</p>
  </>

  );
}
```

얘를 state, useEffect를 옮겨서 만든다.

```js

function useFetchProducts() {
 const [products, setProducts] = useState([]);
 
 useEffect(() => {
  const fetchProducts = async () => {
   const url = 'http://localhost:3000/products';
   const response = await fetch(url);
   const data = await response.json();
   setProducts(data.products);
  };

  fetchProducts();
 }, []);
 return products
}

export default function App() {
 const products = useFetchProducts()
 
  return (
  <>
   <TimerControl />
   <p>Hello, world!</p>
  </>

  );
}
```

~src/hooks/useFetchProducts 에 담아줌 ;;

```js
// useFetchProducts.ts
import { useEffect, useState } from "react";

export default function useFetchProducts() {
 const [products, setProducts] = useState([]);
 
 useEffect(() => {
  const fetchProducts = async () => {
   const url = 'http://localhost:3000/products';
   const response = await fetch(url);
   const data = await response.json();
   setProducts(data.products);
  };

  fetchProducts();
 }, []);
 return products
}
```

```js
// App.tsx
import { useEffect, useState } from "react";
import TimerControl from "./components/TimerControl";
import useFetchProducts from "./hooks/useFetchProducts";


export default function App() {
 const products = useFetchProducts()

  return (
  <>
   <TimerControl />
   <p>Hello, world!</p>
  </>

  );
}
```

setState 함수는 숨기는 형태. 캡슐화한 형태이다.

* 컴포넌트 코드는 명확해지고 setState 함수가 실수로 잘못 쓰일 문제를 해소.

* 여러 state를 한번에 묶는것도 가능.

<br>

## Hook 규칙

Hook 호출은 규칙이 있어서 단순하게 쓰도록 노력해야 한다.

1. Function Component 바로 안쪽(함수의 최상위)에서만 호출.
    조건문이나 반복문 안에서 하면 안됨.
    순서 보장이 중요하기 때문이다. 함수는 실제로 상태를 가질 수 없지만 react hook은 index 기반의 외부 상태에 값을 저장해놓고
    마치 그 함수가 상태를 저장하는 것 처럼 효과를 내는 원리이기 때문이다.

[hook에 제약이 있는 이유](https://github.com/heyho00/make-react/blob/main/src/react.js)

2. Function Component 또는 Custom Hook에서만 호출.
    함수형 컴포넌트가 상태를 가질 수 있도록 만든 컨셉이기 때문에 당연..
    