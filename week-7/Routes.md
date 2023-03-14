# Routes

[실습 레포](https://github.com/heyho00/routing)

```ts
import { Routes, Route } from "react-router-dom";

function App() {
  return (
    <div>
      <Header />
      <main>
        <Routes>
          <Route path="/" element={<HomePage />} />
          <Route path="/about" element={<AboutPage />} />
        </Routes>
      </main>
      <Footer />
    </div>
  );
}
```

## BrowserRouter

```js
import { BrowserRouter } from "react-router-dom";

root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>
);
```

## MemoryRouter

테스트 코드에선 메모리 라우터를 사용한다.

```js
describe("App", () => {
  function renderApp(path: string) {
    render(
      <MemoryRouter initialEntries={[path]}>
        <App />
      </MemoryRouter>
    );
  }

  context("when the current path is “/”", () => {
    it("renders the home page", () => {
      renderApp("/");

      screen.getByText(/Hello/);
    });
  });

  context("when the current path is “/about”", () => {
    it("renders the about page", () => {
      renderApp("/about");

      screen.getByText(/About/);
    });
  });
});
```
