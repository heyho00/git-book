# Routing

[실습 레포](https://github.com/heyho00/routing)

웹 사이트는 URL에 따라 다른 웹 페이지를 보여준다.

하나의 웹 페이지를 하나의 컴포넌트로 만들고, URL에 따라 적절한 컴포넌트가 보이게끔 구현한다.

```ts
function App() {
  const { pathname } = window.location;

  return (
    <div>
      <Header />
      <main>
        {pathname === "/" && <HomePage />}
        {pathname === "/about" && <AboutPage />}
      </main>
      <Footer />
    </div>
  );
}
```

## HTML DOM API

우선 Dom은 웹에서 `문서의 구조`와 콘텐츠를 구성하는 `객체의 데이터 표현`입니다. (document object model)

dom은 웹 문서를 위한 프로그래밍 `인터페이스`이다.

dom api를 이용해 조작한다.

## Location

Location 인터페이스는 객체가 연결된 장소(URL)를 표현한다.

Location 인터페이스에 변경을 가하면 연결된 객체에도 반영되는데,

`Document`와 `Window` 인터페이스가 이런 Location을 가지고 있습니다. 각각 Document.location과 Window.location으로 접근할 수 있다.

- Location.href (en-US)
  온전한 URL을 값으로 하는 `DOMString`. 바뀔 경우 연결된 문서도 새로운 페이지로 이동합니다. 연결된 문서와 다른 오리진에서도 설정할 수 있습니다.

- Location.host (en-US)
  URL의 호스트 부분을 값으로 하는 DOMString으로, 호스트명, ':', 포트 번호를 포함.

- Location.pathname (en-US)
  '/' 문자 뒤 URL의 경로를 값으로 하는 DOMString입니다.
