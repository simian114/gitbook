# Indexed Access Types

`Indexed access type` 은 다른 타입의 특정한 `property` 를 볼 때 유용하게 사용할 수 있다. 말만 들어서는 잘 모르니 예시를 보자.

```typescript
type Person = { age: number; name: string; alive: boolean };
type Age = Person['age']; // type Age = number
```

**인덱싱 된 타입은 그 자체로 타입이 된다.** 무슨 말이냐하면 `obj[key]` 로 오브젝트에 `[]` 연산자를 하면 그 `value` 가 나오는것처럼 타입에 `[]` 연산자를 사용하면 즉 인덱싱으로 접근하면 **그 값으로 타입이 나온다는 의미다.**

예시를 더 보면 \(위의 Person을 그대로 사용한다.\)

```typescript
type I1 = Person["age" | "name"]; // type I1 = string | number

type I2 = Person[keyof Person]; // type I2 = string | number | boolean

type AliveOrName = "alive" | "name";
type I3 = Person[AliveOrName]; // type I3 = string | boolean

// 만약 Person에 없는 키로 접근한다면? 에러가 발생!
type Error = Person['ali'] // 에러발생!
```

### 배열은 그러면 어떻게 될까?

배열이란 무엇인가? 배열은 객체의 특수한 형태다. 그렇기 때문에 객체에서 사용하는 `[]` 같은 연산자를 그대로 사용할 수 있다. 다만 키 값으로 0 이상의 정수가 온다는 제한이 있을 뿐이다.

자 그러면 배열안에 있는 값들의 타입을 알기 위해서는 어떻게 해야할까? 아주 간단하다. `number` 라는 값을 사용하면 된다!

```typescript
const MyArray = [
  { name: "Alice", age: 15 },
  { name: "Bob", age: 23 },
  { name: "Eve", age: 38 },
];

type Person = typeof MyArray[number];

// type Person = {
//     name: string;
//     age: number;
// }
```

