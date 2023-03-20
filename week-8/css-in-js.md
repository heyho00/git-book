# CSS in JS

CSS-in-JS는 JavaScript를 사용하여 구성 요소의 스타일을 지정하는 스타일 지정 기술.

## CSS의 문제점으로 볼 수 있다

1. Global namespace
   모든 스타일이 global에 선언되어 중복되지 않는 class 이름을 적용해야 함.

2. Dependencies
   css간 의존 관계 관리가 힘듬

3. Dead Code Elimination
   기능 추가, 변경, 삭제 과정에서 불필요한 css를 제거하기 어려움

4. Minification
   클래스 이름의 최소화가 어려움

5. Sharing Constants
   JS 코드와 상태 값을 공유할 수 없음

6. Non-deterministic Resolution
   CSS 로드 순서에 따라 스타일 우선 순위가 달라짐

7. Isolation
   CSS와 JS가 분리된 탓에 상속에 따른 격리가 어려움

**보완하고자 CSS in CSS, CSS in JS 방식이 등장.**

## CSS in CSS

SASS, CSS Modules가 있다.

동일한 네임 스페이스를 공유하는 속성들을 관리하기 용이. (nested 구조)

! 변수 사용으로 중복 코드 관리 용이.

```js
$bg-color: blue;
$bg-img: "/assets/images/";

.box {
  background: $bg-color url($bg-img + "bg.jpg");
}
```

! mixin 을 통해 스타일 재사용성을 높임

```js
@mixin replace-text($image, $x: 50%, $y: 50%) {
  text-indent: -99999em;
  overflow: hidden;
  text-align: left;

  background: {
    image: $image;
    repeat: no-repeat;
    position: $x $y;
  }
}

.mail-icon {
  @include replace-text(url("/images/mail.svg"), 0);
}
```

하지만 여전히 클래스 중복의 문제점을 해결해주진 못함.

## CSS Modules 의 장점

- 중복 클래스명을 더 이상 신경쓰지 않아도 됨( 모든 CSS 선택자에 고유한 해시값을 부여함으로써 속성 중첩을 방지함 )

```js
// App.jsx
// 아래와 같이 import 해서 사용
import styles from "./styles.module.css";

function App() {
  return (
    <div id={styles.container}>
      <button className={styles.button}>버튼</button>
    </div>
  );
}

export default App;
```

단점.

중복 클래스를 써도 상관 없으니 클래스명을 성의없이 작성해도 동작한다

컴포넌트별 css파일을 생성하다보니 규모가 커지면 너무 많은 css 파일을 관리해야함.

## CSS in JS 장점

CSS를 컴포넌트 레벨로 추상화한다

JS 환경을 최대한으로 활용 가능하다

CSS 코드 경량화( BEM 등으로 클래스 명이 길어지더라도 자동으로 짧은 길이의 유니크한 클래스를 생성한다 )

## styled components 단점

러닝 커브.

컴포넌트가 렌더링 될 때 CSS가 적용되기 때문에, FOUC 가 발생할 수 있다
`( FOUC( Flash of unstyled content )` : 스타일 시트가 적용되기 전 마크업 된 그대로의 모습이 잠깐 보이는 현상

별도의 라이브러리 설치에 따라 번들 크기 증가, CSS-in-CSS에 비해 느린 속도
