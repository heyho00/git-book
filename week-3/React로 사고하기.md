# React로 사고하기

## Step 1: UI를 구성 요소 계층 구조로 나누기

JSON이 잘 구조화되어 있으면 자연스럽게 UI의 구성 요소 구조에 매핑되는 경우가 많다.

UI와 데이터 모델은 종종 동일한 정보 아키텍처, 즉 동일한 형태를 갖기 때문.

각 구성 요소가 데이터 모델의 한 부분과 일치하는 구성 요소로 UI를 분리한다.

```json
[
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$1", stocked: true, name: "Dragonfruit" },
  { category: "Fruits", price: "$2", stocked: false, name: "Passionfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  { category: "Vegetables", price: "$4", stocked: false, name: "Pumpkin" },
  { category: "Vegetables", price: "$1", stocked: true, name: "Peas" }
]
```

<img src="https://beta.reactjs.org/images/docs/s_thinking-in-react_ui_outline.png" alt="structure" width="500" />

```json
// 데이터 구조는 front에서 어떻게 뿌려질지에 따라 더 좋게 바꿀 수 있다. 이런식?

[
    {category:[
        fruits: [{id:1, name:'melon'}, ...],
        vegetables: [{id:1, name:'spinach'}, ...]
    ]}
]
```

## Step 2: React에서 정적 버전 빌드

interaction을 배제한 ui 렌더링만 하는 버전을 먼저 빌드,

그 다음 상호 작용 기능을 추가하는 방식이 더 쉬운 경우가 많다.

데이터를 전달하는 구성요소를 빌드하는데 두 가지 방식이 있다.

간단한 예에서는 일반적으로 `하향식`으로 진행하는 것이 더 쉽고 대규모 프로젝트에서는 `상향식`으로 진행하는 것이 더 쉽습니다.

구성 요소를 빌드하고 나면 재사용 가능한 구성 요소가 보인다.

데이터 모델은 최상위에서 맨 아래로 흐르는 단방향 데이터 흐름의 모습을 갖는다.

## + 컴포넌트 계층 구조

컴포넌트 계층 구조에 관해 몇가지 기준이 있다.

1. SRP(Single Responsibility Principle)

`단일 책임 원칙`이다. 컴포넌트가 하나의 책임만 가지며 제공하는 모든 기능은 이 책임과 깊게 부합해야 한다.

컴포넌트와 함수도 포함해 너무 하는일이 많아지며 안된다는 의미이다. 수정에 대응하기 힘들기 때문에.
작은 컴포넌트(일종의 부품)을 만들어 조립하는 개념이다.

## Extract function

일단 길게 코드를 작성하고, 적절히 자를 수 있는 부분이 보일 때 `함수로 추출`한다.

바로 다른 파일로 떼어내야 한다는 강박을 버리고 함수 정도로 추출할 수 있다.

```js
function printOwing(invoice) {
  printBanner();
  let outstanding  = calculateOutstanding();

  //print details
  console.log(`name: ${invoice.customer}`);
  console.log(`amount: ${outstanding}`);  
}

⬇

function printOwing(invoice) {
  printBanner();
  let outstanding  = calculateOutstanding();
  printDetails(outstanding);

  function printDetails(outstanding) {
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${outstanding}`);
  }
}
```

## props는 읽기 전용이다

props는 수정해서는 안된다.

상호작용은 상태 변경으로 데이터를 수정한다.

```js
// 순수 함수가  있다.

function sum(a, b) {
    return a + b
}

// 입력값을 바꾸려 하지않고 항상 동일한 입력에 동일한 결과를 반환한다.

function withdraw(account, amount) {
    account.total -= amount
}

// 자신의 입력값을 변경하는 위 함수는 순수 함수가 아니다.
```

React는 유연하지만 한가지 엄격한 규칙이 있는데,

**자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 한다.**

props 바꾸지 말고 동적인 애플리케이션 UI를 만들기 위해 state를 사용하라는 의미.

<br>

## Step 3: UI 상태의 최소이지만 완전한 표현 찾기

상호작용적인 UI를 만들기 위해선 사용자가 데이터 모델을 변경할 수 있어야 한다. 이를 위해 `상태`를 쓴다.

이 상태를 구조화하는 가장 중요한 원칙은 `DRY (don't repeat yourself)`를 유지하는 것.

`DRY` 원칙을 따르는 `SSOT(Single Source of Truth)`를 만든다.

### DRY

모든 형태의 정보 중복을 지양하는 원리.

### SSOT

단일 진실 공급원.

모든 데이터 요소를 한 곳에서만 제어, 편집하도록 조직하는 형태를 의미.

데이터가 위치한 다른 모든 곳들은 `진실 공급원`의 위치를 참조하기만 한다.

진실 공급원의 위치에서 데이터 요소를 갱신하면 수정 사항 반영을 빠뜨릴 데이터의 사본이 존재할 가능성 없이 시스템 전체에 전파되게 된다.

### 상태의 조건

1. 변하는 값이어야 함. 자체가 변경을 다루기 위한 요소임.
2. props로 부모로부터 전달되지 않음. (당연..그건 Props니까...)
3. 기존 상태 또는 props를 기반으로 계산할 수 있는 값이 아니어야 함.

공식 문서의 예제를 보면,

1. 제품의 목록은 props로 전달되므로 상태가 아니다.
2. 사용자가 입력하는 검색어는 시간이 지남에 따라 변경되고 어떤것에서도 계산할 수 없으니 상태이다.
3. 확인란도 마찬가지로 상태이다.
4. 필터링 된 제품 목록은 원래 목록을 가져와 검색 텍스트 및 확인란의 값에 따라 필터링하여 계산한 결과이므로 상태가 아니다.

## Step 4: state가 있어야 할 위치 파악

React는 단방향 데이터 흐름을 사용하므로 state가 영향을 미쳐야할 컴포넌트들의 최상단, 즉 공통적인 부모 컴포넌트에 state가 위치해야 한다.

state가 바뀌면 하위 컴포넌트들이 rerender 되는 개념.

## Step 5: 역방향 데이터 흐름 추가

사용자가 state를 바꿀 수 있도록 하려면 setState함수(useState 훅이 제공하는 헬퍼함수)를 마찬가지로 하위 컴포넌트로 전달해 그 함수를 이용함으로써 state를 바꿀 수 있게 한다.

함수가 일급 객체라서 다른 함수에 함수를 인자로 넘겨주거나, 리턴값으로 함수를 사용할 수 있기 때문이다.
