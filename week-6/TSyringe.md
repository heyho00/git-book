# TSyringe

[실습 레포](https://github.com/heyho00/external-store/tree/external-store)

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
import { singleton } from "tsyringe";

@singleton() // 이런 데코레이터를 써주려면 tsconfig.json 에서 아래 두개 주석을 푼다.
// "experimentalDecorators" : true,
// "emitDecoratorMetadata" : true
class CounterStore {
  // …(중략)...
}
```

싱글톤 CounterStore 객체를 사용:

```ts
import { container } from "tsyringe";

const counterStore = container.resolve(CounterStore);
```

## sington (싱글톤)

객체의 인스턴스가 오직 1개만 생성되는 패턴을 의미한다.

하나의 external store를 여러곳에서 불러 쓰는 개념. 값이나 메서드를 공유함.

### singleton을 사용하는 이유

1. 메모리

최초 한번의 new 연산자를 통해 생성된 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지할 수 있다.

뿐만 아니라 이미 생성된 인스턴스를 활용하니 속도 측면에서도 이점이 있다.

2. 클래스 간에 데이터 공유가 쉽다.

단점

1. 코드 자체가 많이 필요하다. 앞서 소개한 구현 방법외에도 정적 팩토리 메서드에서 객체 생성을 확인하고 생성자를 호출하는 경우에

멀티스레딩 환경에서 발생할 수 있는 동시성 문제 해결을 위해 syncronized 키워드를 사용해야 한다.

2. 테스트하기 어렵다.

싱글톤 인스턴스는 자원을 공유하고 있기 때문에 테스트가 결정적으로 격리된 환경에서 수행되려면 매번 인스턴스의 상태를 초기화시켜주어야 한다.

그렇지 않으면 어플리케이션 전역에서 상태를 공유하기 때문에 테스트가 온전하게 수행되지 못한다.

이외에도 자식클래스를 만들수 없다는 점과, 내부 상태를 변경하기 어렵다는 점 등 여러가지 문제들이 존재한다.

결과적으로 이러한 문제들을 안고있는 싱글톤 패턴은 유연성이 많이 떨어지는 패턴이라고 할 수 있다.

오직 한 개의 인스턴스 생성을 보증하여 효율을 찾을 수 있지만 그에 못지많게 수반되는 문제점도 많다.

싱글톤 패턴은 안티패턴으로 불릴 만큼 단독으로 사용한다면 객체 지향에 위반되는 사례가 많다.
