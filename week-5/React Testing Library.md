# React Testing Library

[실습레포](https://github.com/heyho00/frontend-survival-week04/tree/tdd)

React 컴포넌트를 사용자 입장에 가깝게 테스트할 수 있는 도구.

when, then의 개념이 있다.

```js
 context('with two numbers', () => {
    it('returns sum of two numbers', () => {
        
        //when
        const result = add(2)

       //then
       expect(result).toBe(2);
    });
  });
```

원래 ReactDom..createRoot() 식으로 돔을 만들어서 렌더해줬다면

그런거 없이 편하게 테스팅 할 수 있는 render를 제공한다.

```js

// TextField.tsx

import { useRef } from 'react';

type TextFiledProps={
    label:string
    placeholder: string
    text:string
    setFilterText:(value:string) => void
}

export default function TextField({
  label, placeholder, text, setFilterText,
}:TextFiledProps) {
  const id = useRef(`input=${Math.random()}`);

  const handleChange = (e:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = e.target;
    setFilterText(value);
  };

  return (
    <div>
      <label htmlFor="input2">
        {label}
      </label>
      <input
        id="input2"
        type="text"
      />
    </div>

  );
}
```

```js
//TextField.test.tsx

import { render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

test('TextField', () => {
  // given
  const setFilterText = () => {
    //
  };
  // when
  render(
    <TextField
      label="Name"
      placeholder=""
      text=""
      setFilterText={setFilterText}
    />,
  );

  // then 화면이 나와야 함
  screen.getByLabelText('Name');
  // 레이블이 Search인 인풋이 잡힌다.
});


```

label text가 Search인 인풋을 잡는데,

label의 htmlFor와 input의 id가 같아야 한다. 그래야 둘이 연결된 Element로 인식한다.

그렇지 않으면 getByLabelText로 찾지 못함.

완전히 일치하지 않아도 get으로 가져오고 싶다면 정규 표현식을 이용한다.

```js
// ex

screen.getByLabelText(/Sear/)
```

아래처럼 get해온 element를 테스트 할수도 있다.

```js

  //TextField.text.tsx
  const input = screen.getByLabelText('Name');
  expect(input.value).toBe('Tester');
  // or screengetByDisplayValue('Tester')

  // TextField.tsx
    return (
    <div>
      <label htmlFor="input2">
        {label}
      </label>
      <input
        id="input2"
        type="text"
        value="Tester"
      />
    </div>

  );
```

## 변수처리 해줄 수 있다

```js
//TextField.test.tsx
test('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = () => {
    //
  };

  // when
  render(
    <TextField
      label={label}
      placeholder=""
      text={text}
      setText={setText}
    />,
  );
  // then 
  screen.getByLabelText('Name');
  screen.getAllByDisplayValue(text); // value가 text인 인풋을 가져옴
});



//TextField.tsx
import { useRef } from 'react';

type TextFiledProps={
    label:string
    placeholder: string
    text:string
    setText:(value:string) => void
}

export default function TextField({
  label, placeholder, text, setText,
}:TextFiledProps) {
  const id = useRef(`input=${Math.random()}`);

  const handleChange = (e:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = e.target;
    setFIlter(value);
  };

  return (
    <div>
      <label htmlFor={`${id}`}>
        {label}
      </label>
      <input
        id={`${id}`}
        type="text"
        value={text}
        placeholder={placeholder}
        onChange={handleChange}
      />
    </div>

  );
}

```

## event

여기까지 상태에서 보면 handleChange 내에서 setFilter 등 아무런 함수를 넣어도

테스트를 작성하지 않아서 아무런 이상이 없다.

이것도 잡아주고 싶다.

```js

// TextField.test.tsx
//테스트를 추가했다.

  const input = screen.getByLabelText('Name');
  fireEvent.change(input, {
    target: { value: 'New Name' },
  });


// TextField.tsx

  const handleChange = (e:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = e.target;
    setFilter(value); // 이 부분이 에러.
    // ReferenceError: setFilter is not defined
    
    // 이벤트가 끝났을때 input 의 e.target.value가 바뀌어야 하는데 
    // 그렇게 할 setFilter 함수가 정의되지 않아서.

    // 그래서 setText(value) 로 바꿔주면 에러는 해결된다.
    // setText 함수도 내부 구현이 되어있지는 않아도 말이다.
    
    // setText() 이렇게 value를 전달하지 않아도 에러는 없다.
    // 딱 handleChange 함수 안에 에러가 있는지 없는지만 판별.
  };

  return (
    <div>
      <label htmlFor={`${id}`}>
        {label}
      </label>
      <input
        id={`${id}`}
        type="text"
        value={text}
        placeholder={placeholder}
        onChange={handleChange}
      />
    </div>
  )
```

## setText가 불렸는지 확인하고싶어

```js
import { fireEvent, render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

test('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  let called = false; // 추가
  const setText = () => { // setText가 실행되면 일어날 effect 
    called = true;
  };
  // when
  render(
    <TextField
      label={label}
      placeholder="name is harry"
      text={text}
      setText={setText}
    />,
  );

  screen.getByLabelText('Name');
  screen.getAllByDisplayValue(text);
  screen.getAllByPlaceholderText(/name/);

  const input = screen.getByLabelText('Name');
  fireEvent.change(input, {
    target: { value: 'New Name' },
  });

  expect(called).toBeTruthy(); // 추가 : called 가 true일때만 통과
});


// TextField.tsx

export default function TextField({
  label, placeholder, text, setText,
}:TextFiledProps) {
  const id = useRef(`input=${Math.random()}`);

  const handleChange = (e:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = e.target;
    // setText(value); 
    // 주석처리로 함수 실행되지 않아 에러남.
  };

  return (
    <div>
      <label htmlFor={`${id}`}>
        {label}
      </label>
      <input
        id={`${id}`}
        type="text"
        value={text}
        placeholder={placeholder}
        onChange={handleChange}
      />
    </div>

  );

```

## jest.fn 목킹

```js

  let called = false;
  const setText = jest.fn(); 
  // 이렇게만 해도 된다는데 안되는걸?

  //무슨일이 일어나는지 직접 정의해도 됨.
  const setText = jest.fn(()=>{
    called = true
  }); 
```

이것보다 다른 방식으로 한다.

## setText가 불렸는지, 원하는 값으로 바꾸게 하는지까지 테스트하고 싶어

setText가 call됐는지 바로 확인할 수 있다.

```js
//TestField.test.tsx
import { fireEvent, render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

test('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  // when
  render(
    <TextField
      label={label}
      placeholder="name is harry"
      text={text}
      setText={setText}
    />,
  );

//이 input, fireEvent 부분을 주석하면 toBeCalledWith 부분이 통과되지 않는다.  fireEvent에 대해 정확히 더 알아보자.
  const input = screen.getByLabelText('Name');
  fireEvent.change(input, {
    target: { value: 'New Name' },
  });

  expect(setText).toBeCalled(); // 실행여부
  expect(setText).toBeCalledWith('New Name') // New Name 나오는지

});

//TextField.tsx
.
.
.

  const handleChange = (e:React.ChangeEvent<HTMLInputElement>) => {
    const { value } = e.target;
    // setText(value);
  };

  return (
    <div>
      <label htmlFor={`${id}`}>
        {label}
.
.
.

```

## context 활용

```js
import { fireEvent, render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

const context = describe;

describe('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  it('renders elements', () => {
    // when
    render(
      <TextField
        label={label}
        placeholder="name is harry"
        text={text}
        setText={setText}
      />,
    );

    // then 화면이 나와야 함
    screen.getByLabelText(label);
    screen.getAllByDisplayValue(text);
    screen.getAllByPlaceholderText(/name/);
  });

  // --------

  // context: 입력했을때
  context('when user enters name', () => {
    it('calls "setText" handler', () => {
      // given
      render(
        <TextField
          label={label}
          placeholder="name is harry"
          text={text}
          setText={setText}
        />,
      );

      // when
      const input = screen.getByLabelText('Name');
      fireEvent.change(input, {
        target: { value: 'New Name' },
      });

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
});

```

### 중복되는 render 부분을 빼준다

```js
import { fireEvent, render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

const context = describe;

describe('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  function renderTextField() {
    render(
      <TextField
        label={label}
        placeholder="name is harry"
        text={text}
        setText={setText}
      />,
    );
  }

  it('renders elements', () => {
    // when
    renderTextField();

    // then
    screen.getByLabelText(label);
    screen.getAllByDisplayValue(text);
    screen.getAllByPlaceholderText(/name/);
  });

  // --------

  context('when user enters name', () => {
    it('calls "setText" handler', () => {
      // given
      renderTextField();

      // when
      const input = screen.getByLabelText('Name');
      fireEvent.change(input, {
        target: { value: 'New Name' },
      });

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
});

```

### mock clear

여기서 it('renders elements')와

context를 이용한 부분 두 군데에서 각자 따로

setText 목을 사용하는데

어디서든 콜 하기만 하면 콜 된거.. 그러면 안돼서

매 테스트마다 초기화 해줘야 한다.

```js
.
.
.

describe('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  beforeEach(() => { //둘중에 나 하면 됨.
    setText.mockClear();
    // jest.clearAllMocks();
  });

  function renderTextField() {
    render(
      <TextField
        label={label}
        placeholder="name is harry"
        text={text}
        setText={setText}
      />,
.
.
.
```

아래처럼 정리할 수 있다.

context 내부에서 드러내고 싶으면 그냥 드러내도 좋다.

```js
import { fireEvent, render, screen } from '@testing-library/react';
import TextField from '../src/components/TextField';

const context = describe;

describe('TextField', () => {
  // given
  const label = 'Name';
  const text = 'Tester';

  const setText = jest.fn();

  beforeEach(() => {
    // setText.mockClear();
    jest.clearAllMocks();
  });

  function renderTextField() {
    render(
      <TextField
        label={label}
        placeholder="name is harry"
        text={text}
        setText={setText}
      />,
    );
  }

  function inputText(value:string) {
    fireEvent.change(screen.getByLabelText('Name'), {
      target: { value },
    });
  }

  it('renders elements', () => {
    // when
    renderTextField();

    // then 화면이 나와야 함
    screen.getByLabelText(label);
    screen.getAllByDisplayValue(text);
    screen.getAllByPlaceholderText(/name/);
  });

  // --------

  // context: 입력했을때
  context('when user enters name', () => {
    beforeEach(() => { //given이 it 안에 있는게 싫어서 뺌
      // given
      renderTextField(); 
    });

    it('calls "setText" handler', () => {
      // when
      inputText('New Name'); //renderTextField 처럼 빼줬다.

      // then
      expect(setText).toBeCalledWith('New Name');
    });
  });
});

```

반복되는 코드를 `Extract Function`하고,

`fireEvent` 등을 통해 인터랙션만 검증한다.

<br>

## 외부 의존성이 큰 코드를 작성한다면 해당 부분만 가짜로 구현할 수 있다

```js
// App.test.tsx
import { render, screen } from '@testing-library/react';
import App from './App';

jest.mock('./hooks/useFetchRestaurants.ts', () => () => [
  {
    id: '1',
    category: '중식',
    name: '메가반점',
    menu: [
      { id: '1', name: '짜장면', price: 8000 },
      { id: '2', name: '짬뽕', price: 8000 },
      { id: '3', name: '차돌짬뽕', price: 9000 },
      { id: '4', name: '탕수육', price: 14000 },
    ],
  },
]);

test('App', () => {
  render(<App />);

  screen.getByText('메가반점');
});

```

express 서버를 실행시키지 않고 mock을 활용해 테스트할 수 있다.

아래처럼 따로 빼서 목관리를 할 수 있다.

```js

// fixtures/products.ts
const products = [
  {
    id: '1',
    category: '중식',
    name: '메가반점',
    menu: [
      { id: '1', name: '짜장면', price: 8000 },
      { id: '2', name: '짬뽕', price: 8000 },
      { id: '3', name: '차돌짬뽕', price: 9000 },
      { id: '4', name: '탕수육', price: 14000 },
    ],
  },
  {
    id: '2',
    category: '한식',
    name: '메리김밥',
    menu: [
      { id: '5', name: '김밥', price: 3500 },
      { id: '6', name: '참치김밥', price: 4500 },
      { id: '7', name: '제육김밥', price: 5000 },
      { id: '8', name: '훈제오리김밥', price: 5500 },
    ],
  },
  {
    id: '3',
    category: '일식',
    name: '혹등고래카레',
    menu: [
      { id: '9', name: '기본카레', price: 9000 },
      { id: '10', name: '가라아게카레', price: 14000 },
      { id: '11', name: '소시지카레', price: 13000 },
      { id: '12', name: '돈까스카레', price: 14000 },
      { id: '13', name: '닭가슴살카레', price: 13000 },
    ],
  },
];
export default products;


// fixtures/index.ts
import products from './products';

export default {
  products,
};


//App.test.tsx
import { render, screen } from '@testing-library/react';
import fixtures from '../fixtures';
import App from './App';

jest.mock('./hooks/useFetchRestaurants.ts', () => () => fixtures.products);

test('App', () => {
  render(<App />);

  screen.getByText('메가반점');
});

```

### hooks Mock을 아예 따로 뺄 수 있다

```js
// App.test.tsx
import { render, screen } from '@testing-library/react';
import fixtures from '../fixtures';
import App from './App';

jest.mock('./hooks/useFetchRestaurants.ts');
// 함수로 전달했던 목을 따로 뺌.

test('App', () => {
  render(<App />);

  screen.getByText('메가반점');
});

// hooks/__mocks__/useFetchRestaurants.ts
import fixtures from '../../../fixtures';
const useFetchProducts = jest.fn(() => fixtures.products);
export default useFetchProducts;

```

jest.fn없이 () => fixtures.products 만 써줘도 가능.

그렇지만 써주면 가짜라는 사실이 더 명확하고

호출이 됐는지 확인하는 등의 test를 또 활용할 수 있기 때문에 붙이는 편을 추천.

<br>

점점 앱이 커지면서 하나씩 가짜 구현으로 하기 어려울 때가 있다.

이럴때 MSW등의 대안을 고려해볼 수 있다. 다음 주제로 넘어가 보자.