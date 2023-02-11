# e2e

jest 유닛테스트를 배웠는데,

이번 과제엔 codecept를 이용한 e2e test가 포함되어 있었다.

## E2E Test란(End To End Test)

E2E Test는 종단(Endpoint) 간 테스트로 사용자의 입장에서 테스트 하는 것 입니다.

보통 Web, App 등에서 GUI를 통해서 시나리오, 기능 테스트 등을 수행합니다.

사용자에게 직접 노출되는 부분을 점검한다.

프론트엔드에서는 원하는 Text가 제대로 나오는지, A를 클릭했을때 기대하는 동작을 하는지 등 을 테스트한다.

## 왜 E2E 테스트를 해야하는가

### 1. qa가 제품 관리를 잘할 수 있음

qa에서 시스템 테스트를 진행하지만 단순 반복적인 테스트를 항상 진행하는것은 효율적이지 않다.

이런 영역들을 자동화할 수 있다.

사용자의 동작을 모사해야 하기 때문에 사용자 입장의 workflow와 laytency를 측정하고 점검할 수 있다.

### 2. 테스트 코드를 작성할 공수가 제한적이라면 E2E 테스트만

종단에서 테스트를 통과하면 기능이 잘 동작하는 것, 종단에서 실패하는 테스트가 진짜 문제.

### E2E Test Framework

실습에 세팅된 codecept말고도 Selenium, Cypress, TestCafe 등이 있다.

## 실습 test code

```js
Feature('Home');

Scenario('Visit the home page', ({ I }) => {
  I.amOnPage('/');

  I.see('Hello, wholeman!');

  I.seeElement('//img[@alt="Test Image"]');
});

Scenario('Counter test', ({ I }) => {
  I.amOnPage('/');

  I.see('Count: 0');

  I.click('+1');

  I.see('Count: 1');

  I.click('+2');

  I.see('Count: 3');

  I.click('+4');

  I.see('Count: 7');

  I.click('+5');

  I.see('Count: 12');

  I.click('+3');

  I.see('Count: 15');
});

```

이런식으로 user scenario를 작성한다.
