---
description: 들어오는 input 에 따라 타입을 결정하는건 아주 중요한 작업이다. 꼭 내껄로 만들자
---

# 조건부타입 \(Conditional types\)

> [타입스크립트 핸드북을 참고하자.](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)

### 삼항연산자와 비슷하다

조건에 의한 타입결정은 일반 프로그래밍 언어의 **삼항연산자와 비슷하다.** 아래의 형태로 사용한다.

```typescript
 SomeType extends OtherType ? TrueType : FalseType;
```

만약 `SomeType` 이 `OtherType` 에 `assignable` 하다면, `TrueType` 이 되고 아니라면 `FalseType` 이 된다.

### 제한을 걸어보자

`message` 라는 `prop` 을 가지고 있는 타입에서 해당 `message` 타입을 가져오는 경우를 생각해보자.

```typescript
type MessageOf<T> = T["message"]; // 에러 발생
```

이 코드에서 에러가 나오는 이유는 `T` 라는 타입에 `message` 의 존재여부를 알지 못하기 때문이다. 여기서 우리는 **Constraints 를 만듦으로써 에러를 해결할 수 있다.**

```typescript
type MessageOf<T extends { message: unknown }> = T["message"]
```

앞에 제한을 넣음으로써 빨간줄은 해결했다. 다만 이렇게 만들면 **사용성이 떨어진다.** 왜? 사용에 있어 무조건 `message` 라는 프롭이 있어야지만 실행이 되기 때문이다. 따라서 이번에는 `message` 가 없으면 `never` 를 나오게 수정해보자.

```typescript
type MessageOf<T> = T extends { message: unknown } ? T["message"] : never
```

### 추론을 알아보자

이번에는 `Array` 를 Flatten 하게 만드는 조건부타입을 만들어보자. 만약 `string[]` 라는 타입을 `string` 으로 `Flatten` 하고 싶다면 아래처럼하면된다.

```text
type Flatten<T> = T extends unknown[] ? T[number] : T;
```

이렇게 해도 되지만 지금 문제는 `T[number]` 처럼 `indexed Access Types` 를 사용한다는 것이다. \([Indexed Access Types 가 뭐야?](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)\)

이렇게 직접적으로 접근하는 방법보다 더 좋은 방법이 있는데 바로 **내부 타입을 타입스크립트한테 추론을 맡기는 것이다.** 추론을 맡기는 방법은 굉장히 직관적이고 쉽다. 바로 `infer` 키워드를 사용하는 것. `infer` 키워드를 사용해서 개선해보자.

```typescript
type Flatten<T> = T exnteds Array<infer R> ? R : T;
```

`infer` 를 이용해서 `Return` 타입을 동적으로 결정하는 타입을 만들어보자.

```typescript
type GetReturnType<t> = T extends ( ...args: never[]) => infer Return ? Return : never
```

### Distribute Conditional Types

> 분배 조건 타입

이름이 뭔가 이상해보이는데, 요지는 간단하다. **유니온 타입으로 들어온 타입들에 대해서, 하나하나 적용해 나간다는 의미다.** 예시를 보자.

```typescript
type ToArray<T> = T extends any ? T[] | never;
```

`T` 로 들어온 타입을 배열로 만들어주는 `ToArray`가 있다. 위에서 이야기한 대로 `T` 에 유니언타입을 넣으면 어떻게 될까?

```typescript
// ToArray 의 타입인자로 string | number 을 넣어줌
type StrArrOrNumArr = ToArray<string | number>;

// 결과
type StrArrOrNumArr = string[] | number[]
```

자 대체 왜 `StrArrOrNumArr` 는 `string[] | number[]` 가 되었을까? 이유는 간단하다. `union` 들이 반복문을 돌면서 하나하나 들어갔기 때문이다!!

만약 반복을 돌기 싫고 `(string | number)[]` 과 같은 결과를 얻고 싶다면 `()` 를 잘 사용해주면된다.

```typescript
type ToArrayNonDist<Type> = [Type] extends [any] ? Type[] : never;

// 'StrArrOrNumArr' is no longer a union.
type StrArrOrNumArr = ToArrayNonDist<string | number>;
```

