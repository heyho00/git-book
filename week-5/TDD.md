# TDD (Test Driven Development)

구현보다 인터페이스와 스펙을 먼저 정의, 테스트 코드를 먼저 작성하는 방식이다.

**TDD Cycle**

1. Red → 실패하는 테스트 코드를 작성. 인터페이스와 스펙에 집중한다.

2. Green → 재빨리 테스트를 통과시킨다. 올바른 방법이 아니어도 괜찮다.

3. Refactor → 리팩터링을 통해 코드를 올바르게 만든다. TDD에서 가장 중요한 부분이지만, 간과될 때가 많다.

* 테스트를 기반으로 동작은 바뀌지 않고 리팩토링을 통해 개선시켜 나간다.

* 큰 단위로의 반복은 힘드니, 작은 단계를 만든다.

## Jest

테스트 케이스를 정의할 때 크게 두 가지 방법을 사용한다:

1. test 함수로 개별 테스트를 나열하는 방식.

2. BDD 스타일로 주체-행위 중심으로 테스트를 조직화하는 방식.

### Jest에서 TypeScript 사용하도록 jest.config.js 작성해준다

```js
module.exports = {
 testEnvironment: 'jsdom',
 setupFilesAfterEnv: [
  '@testing-library/jest-dom/extend-expect',
 ],
 transform: {
  '^.+\\.(t|j)sx?$': ['@swc/jest', {
   jsc: {
    parser: {
     syntax: 'typescript', 
     jsx: true,
     decorators: true,
    },
    transform: { 
     react: {
      runtime: 'automatic',
     },
    },
   },
  }],
 },
};


```

App.test.ts나 App.spec.ts 를 이용해 사용한다.
처음에는 test 함수로 개별 테스트를 써볼 수 있다.

```js
function add(x:number, y:number):number {
  return x + y;
}

test('add', () => {
 expect(add(1, 2)).toBe(3);
});

```

이렇게 작성하고 `npx jest` 명령어로 테스트 실행.

계속 실행해야되니 불편하다.

`npx jest --watchAll` 해주면 hot reload 된다.

<br>

## BDD style

BDD 스타일로 `테스트 대상`과 `행위`를 명확히 드러낼 수 있다.

```js
function add(x:number, y:number):number {
  return x + y;
}

describe('add', () => {
  it('두 숫자의 합을 반환한다', () => {
    expect(add(1, 2)).toBe(3);
  });
});


```

### BDD는 쓸데없이 길어지는거 아닌가?

* 한 describe에 다른 describe를 넣어줄 수 있다.

같은 맥락의 테스트를 여럿 추가한다.

테스트를 믿고 add 함수를 refactoring 할 수 있다.

```js
function add(...numbers : number[]):number {
  return numbers.reduce((a, b) => a + b, 0);
}

const context = describe;

describe('add', () => {
 context('with no argument', () => {
  it('returns zero', () => {
   expect(add()).toBe(0);
  });
 });

 context('with only one number', () => {
  it('returns the same number', () => {
   expect(add(1)).toBe(1);
  });
 });

 context('with two numbers', () => {
  it('returns sum of two numbers', () => {
   expect(add(1, 2)).toBe(3);
  });
 });

 context('with three numbers', () => {
  it('returns sum of three numbers', () => {
   expect(add(1, 2, 3)).toBe(6);
  });
 });
});
```

test도 물론 가능하다.

```js
test ('add 테스트', () => {
    expect(add().toBe(0))
    expect(add(1,2).toBe(3))
    expect(add(1,2,3.toBe(6)))
})
```

위의 BDD 방식은 description을 추가해줄 수 있어 좀 더 명확한 것.

<br>

## 단위 테스트란

테스트는 범위나 성격에 따라 `UI Test`, `Integration Test`, `Unit Test` 등으로 나뉜다.

그 중 `unit test`는 작은 단위(함수 단위) 소스 코드의 특정 모듈이 의도된 대로 정확히 작동하는지 검증하는 절차이다.

즉, 모든 `함수`와 `메소드`에 대한 테스트 케이스를 작성하는 절차를 말한다.

다음과 같은 이점이 있다.

1. 작성한 코드가 의도한 대로 작동하는지 검증하기 위한 절차로 문제를 방지하는 기능

2. 코드 변경에 의한 사이드 이펙트를 최대한 줄일 수 있는 예방책이 되기도 한다.

3. 서비스 요구사항 변경이나 리팩토링으로 인해 코드 수정이 필요한 상황에서
    더 유연하고 안정적인 대응을 할 수 있게 된다.

4. 테스트 코드를 작성하는 과정에서 자연스럽게 코드의 모듈화를 고민하게 되는 등의 부수적인 이점들도 있다.
