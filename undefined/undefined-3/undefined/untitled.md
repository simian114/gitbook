# 02. Exclude

{% embed url="https://www.typescriptlang.org/docs/handbook/2/conditional-types.html\#distributive-conditional-types" caption="조건부타입의 Distrubutive를 다시보자" %}

## 문제

Implement the built-in `Exclude<T, U>`. Exclude from `T` those types that are assignable to `U`. For example:

```text
type T0 = Exclude<"a" | "b" | "c", "a">; // expected "b" | "c"
type T1 = Exclude<"a" | "b" | "c", "a" | "b">; // expected "c"
```

## 풀이

문제의 핵심은 `union` 의 `distributive` 다. 바로 풀어보자.

```typescript
type MyExclude<T, U> = ...;

// 1. U 에 조건을 걸어준다.
// union타입에 조건을 걸어주면 distributive 특성이 발생해서 반복이 된다
type MyExclude<T, U> = T extends U ? xxx : yyy;

// 2. 참이면 즉 T 가 U에 있으면 never 를 리턴해준다.
type MyExclude<T, U> = T extends U ? never : T;
```

