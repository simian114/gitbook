# Keyof 타입 오퍼레이터

`keyof` 오퍼레이터는 `object type` 을 받고 리턴타입으로 **string, number 의 union을 리턴한다.**

먼저 자바스크립트에서의 객체에 대해 알아보자. 객체는 아래처럼 구현된다.

```javascript
const obj = {
  name: age,
  1: 3,
    ...
}
```

여기서 내가 말하고 싶은건 **객체의 키 값으로는 문자열 또는 숫자 밖에는 되지 않는다는 사실이다.** 그렇다. 따라서 `keyof` 오퍼레이터로 나오는 타입은 무조건 문자열 또는 숫자일 수 밖에 없다라고 생각할 수 있지만, 또 그렇지만도 않다. 아래의 예시를 보자

```typescript
type Point = { x: number; y: number };
type P = keyof Point;
```

`P` 의 타입도 과연 `string | number` 가 될까? 아니다! `P` 의 타입은 `keyof Point` 가 된다. 그리고 `Point` 의 `keyof` 는 `x | y` 가 된다.

```typescript
type Arrayish = { [n: number]: unknown };
type A = keyof Arrayish; // type A = number

type Mapish = { [k: string]: boolean };
type M = keyof Mapish; // type M = string | number
```

`keyof` 는 특수한 경우에 굉장히 유용하게 사용된다. 특히 [mapped type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) 과 엮일 때 유용하다고 한다.

