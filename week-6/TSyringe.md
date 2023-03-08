# TSyringe

TypeScript용 DI 도구(IoC Container). (의존성 주입)

External Store를 관리하는데 활용할 수 있다. React 컴포넌트 입장에서는 “전역”처럼 여겨진다.

`“Prop Drilling”` 문제를 우아하게 해결할 수 있는 방법 중 하나(React로 한정하면 Context도 쓸 수 있다).

컨텍스트보다 효율적이라함.

## 설치

```js
npm i tsyringe reflect-metadata

core-js, reflection등을 reflect-metadata 대신 쓸수도 있다.
```

싱글톤으로 관리할 CounterStore 클래스를 준비:

```tsx
import { singleton } from 'tsyringe';

@singleton() // 이런 데코레이터를 써주려면 tsconfig.json 에서 아래 두개 주석을 푼다.
// "experimentalDecorators" : true,
// "emitDecoratorMetadata" : true
class CounterStore {
	// …(중략)...
}
```

싱글톤 CounterStore 객체를 사용:

```ts
import { container } from 'tsyringe';

const counterStore = container.resolve(CounterStore);
```

* sington (싱글톤)
