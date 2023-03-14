# Navigation

[실습 레포](https://github.com/heyho00/routing)

## History.pushState

a 태그를 이용할때, 페이지 새로고침이 되지 않고 이동하도록 이렇게 했었음.

```js
const state = {};
const title = "";
const url = "/about";

history.pushState(state, title, url);
```

## Link

```js
function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          <li>
            <Link to="/about">About</Link>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

## NavLink

NavLink를 이용하면 선택된 태그의 className에 active가 붙는다. 이걸 이용해 스타일링.

```js
function Header() {
  return (
    <header>
      <nav>
        <ul>
          <li>
            <NavLink to="/">Home</NavLink>
          </li>
          <li>
            <NavLink to="/about">About</NavLink>
          </li>
        </ul>
      </nav>
    </header>
  );
}
```

## Navigate

묻지도 따지지도않고 리다이렉트함.

테스트에서 “ReferenceError: Request is not defined” 에러가 나면 `“whatwg-fetch”`를 임포트해서 해결할 수 있다.

```js
import { Navigate } from "react-router-dom";

export default function LoginPage() {
  return <Navigate to="/" />;
}
```

## useNavigate

페이지 이동 후 Navigate로 리다이렉트 할 필요없이

바로 이동하는 경우 사용할 수 있다.

```js
import { useNavigate } from "react-router-dom";

export default function Footer() {
  const navigate = useNavigate();

  const handleClick = () => {
    navigate("/about");
  };

  return (
    <footer>
      <button type="button" onClick={handleClick}>
        Press
      </button>
    </footer>
  );
}
```
