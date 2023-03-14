# Router

React Router 버전 6.4부터 지원하는, 라우터 객체를 사용해보자.

라우팅 정보만 별도의 파일로 관리, Outlet에서 분기된다.

```js
import { Outlet } from "react-router-dom";

function Layout() {
  return (
    <div>
      <Header />
      <Outlet />
      <Footer />
    </div>
  );
}

const routes = [
  {
    element: <Layout />,
    children: [
      { path: "/", element: <HomePage /> },
      { path: "/about", element: <AboutPage /> },
    ],
  },
];

export default routes;
```

## App 컴포넌트를 거치지 않고 바로 브라우저 라우터 만들어서 사용할 수 있다

```js
import { createBrowserRouter, RouterProvider } from "react-router-dom";

import routes from "./routes";

const router = createBrowserRouter(routes);

root.render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

## 테스트할때 사용하는 createMemoryRouter

```js
describe("App", () => {
  function renderRouter(path: string) {
    const router = createMemoryRouter(routes, { initialEntries: [path] });
    render(<RouterProvider router={router} />);
  }
  context('when the current path is " / "', () => {
    it("renders the home page", () => {
      renderRouter("/");
      screen.getByText(/Welcome/);
    });
  });

  context('when the current path is " /about "', () => {
    it("renders the about page", () => {
      renderRouter("/about");
      screen.getByText(/This is Test/);
    });
  });
});
```
