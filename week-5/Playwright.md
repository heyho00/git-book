# Playwright

[실습 레포](https://github.com/heyho00/frontend-survival-week04/tree/playwright)

웹 브라우저 기반 E2E 테스트 자동화 도구.

Headless Chrome을 기반으로 한 `Puppeteer를` 계승하면서, 더 많은 웹 브라우저를 지원한다.

## E2E test란

`end to end` 종단 간 테스트로, 프로덕트를 사용자 관점에서 테스트하는 방법이다.

일반적으로 GUI를 통해 시나리오, 기능 테스트 등을 수행.

Endpoint 테스트를 통과하면 기능이 잘 작동한다는 것이므로 모든 테스트를 할 수 없다면 E2E Test만이라도 하는 것이 좋다.

## Headless Chrome

"창이 없다."

브라우저를 이용할 때 기본적으로 창이 뜨고 html, css를 불러와 화면에 그린다.

하지만 이와같은 방식을 사용할 경우 운영체제에 따라 크롬이 실행 될수도, 되지 않을수도 있다.

예를들어 우분투 서버는 '화면'자체가 존재하지 않아 일반적인 방식으로 크롬을 사용할 수 없다.

이를 해결해주는 방식이 바로 `Headless 모드`이다.

브라우저 창을 실제로 운영체제의 창으로 띄우지 않고 화면을 그려주는 작업을 가상으로 진행,

실제 브라우저와 동일하게 동작하지만 창은 뜨지 않는 방식이다.

## 설치

```bash
npm i -D @playwright/test eslint-plugin-playwright
```

### playwright.config.ts 파일

```js
import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
 testDir: './tests',
 retries: 0,
 use: {
  baseURL: 'http://localhost:8080',
  headless: !!process.env.CI,
  screenshot: 'only-on-failure',
 },
};

export default config;
```

### tests/.eslintrc.js 파일

```js
module.exports = {
 env: {
  jest: false,
 },
 extends: ['plugin:playwright/playwright-test'],
 rules: {
  'import/no-extraneous-dependencies': 'off',
 },
};

```

### test/home.spec.ts 생성

```js
import { test, expect } from '@playwright/test';

test('show all products', async ({ page }) => {
  await page.goto('/');

  await expect(page.getByText('푸드코트')).toBeVisible();
  await expect(page.getByText('자장면')).toBeHidden();
});

```

이렇게 하면 chromium이 없다고하면서 통과 안됨.

크롬을 쓰기 위해 설정해준다.

```js
//playwright.config.ts
const config: PlaywrightTestConfig = {
  testDir: './tests',
  retries: 0,
  use: {
    baseURL: 'http://localhost:8080',
    headless: !!process.env.CI,
    screenshot: 'only-on-failure',
    channel: 'chrome', // 추가
  },
};

export default config;

```

서버도 켜고 프론트도 켜고 해야됨.

```js
import { test, expect } from '@playwright/test';

test('show all products', async ({ page }) => {
  await page.goto('/');

  await expect(page.getByText('푸드코트')).toBeVisible();
});

test('Filter products', async ({ page }) => {
  await page.goto('/');

  await expect(page.getByText('푸드코트')).toBeVisible();

  const searchInput = page.getByLabel('검색');

  await searchInput.fill('메');

  await expect(page.getByText('메가반점')).toBeVisible();

  await searchInput.fill('메롱');

  await expect(page.getByText('메가반점')).toBeHidden();

  await searchInput.fill('메');

  await expect(page.getByText('메가반점')).toBeVisible();
});

```

이렇게 시나리오 테스트를 할 수 있다.

### 실행

```js
npx playwright test
```

### headless 실행

브라우저 안뜨고 훨씬 빠르게 검사됨.

playwright.config.ts에 headless: !!process.env.CI,

환경 변수 process.env.CI 이거는

아래처럼 실행할 때 직접 써주면 된다.

```js
CI=true npx playwright test
```

### .gitignore 파일에 에러 상황 스크린샷 담기는 test-results 디렉터리 추가

```bash
/test-results/
```

### icrease button 테스트 예시

```js
test('Click the "increase" button', async({page}) => {
  await page.goto('/')

  const count = 130

  await Promise.all((
    [...Array(count)].map( async ()=> {
      await page.getByText('increase').click()
    })
  ))
})

  await expect(page.getByText(`${count}`)).toBeVisible()
```

## codeceptjs

playwright 보다 좀 더 간단한 라이브러리도 있다. -> codeceptjs

코드가 더 인간 친화적이다.

```js
Feature('과제 테스트');

Scenario('메뉴판 필터링', ({ I }) => {
  I.amOnPage('/');

  I.see('푸드코트 키오스크');

  I.see('메가반점');
  I.see('메리김밥');
  I.see('혹등고래카레');

  I.click('중식');
  I.see('짜장면');
  I.dontSee('김밥');
});

```

기획자와의 소통에서도 활용하면 좋겠다.

## 요점

* 로직을 가능한 많이 분리한다.

* 관심사의 분리를 통해 비즈니스 로직과 나눈다.

* 공용인 부분을 좀 더 빡빡하게 만들기

* 테스트 환경 외에 브라우저에서도 정말로 나오는지 사용자 입장에서 E2E test를 하는게 좋다.
