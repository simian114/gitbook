# Generics

{% embed url="https://www.typescriptlang.org/docs/handbook/2/generics.html" caption="타입스크립트 핸드북 " %}

이전에 `C++` 를 이용해서 `Stack`, `List`, `Map` 등의 컨테이너를 구현했던 경험이 있다. 이런 컨테이너를 구현할 때 가장 중요했던 두가지가 있었는데 첫 번째는 자료구조와 알고리즘, 두번째가 바로 제네릭 이었다.

제네릭을 이용하면 하나의 함수 또는 클래스로 여러 타입의 인자를 다룰 수 있게 된다. 따라서 재사용이 가능하게 되는 엄청난 장점이 있었다. 타입스크립트에도 이런 제네릭이 있다. 당장 학습하지 않을 이유가 없다.

### 제네릭을 왜써? 우리는 any가 있잖아!!

부끄럽지만 제네릭이라는 키워드를 처음봤을 때 이런 생각을 했었다. **제네릭을 왜 쓰지? 그냥 any 를 사용하면 되는거 아니야?** 내 이런 마음을 간파라도 했듯이 공식문서의 Generic 문서 가장 첫 부분에서 이 문제를 짚어준다.

> While using `any` is certainly generic in that it will cause the function to accept any and all types for the type of `arg`, we actually are losing the information about what that type was when the function returns. If we passed in a number, the only information we have is that any type could be returned.

공식문서의 내용을 가져와봤다. 요약하자면 이렇다. **`any` 도 확실히 제네릭이다. 하지만 `any` 는 타입에 대한 정보를 알려주지 못한다!** 예를 들어보자.

```typescript
function identity(arg: any): any {
  return arg;
}
const num = 10;
const ret = identity(num);
```

`ret` 은 어떻게 타입추론이 되었을까? 당연히 `number` 로 되지 않았을까? 당연히 아니다! `ret` 의 타입은 **any다.** 즉 `any` 를 사용하게 되면 타입에 대한 정보를 잃어버리게 되고 따라서 우리가 원하는 바를 이루지 못한다. **즉 any 는 generic 를 대체할 수 없다!**

자 그럼 맛보기로 제네릭을 사용해서 위의 문제를 해결하자.

```typescript
function identity<T>(arg: T): T {
  return arg
}
```

제네릭의 핵심은 **타입에 대한 정보를 기억한다는거다.**

### Generic Types 적용

#### interface

자 이제 그러면 `interface` 에 제네릭을 적용해보자. 위의 `identity` 라는 함수를 `interface` 로 나타내보자.

```typescript
interface GenericIdentityFn<T> {
  (arg: T): T;
}

function identity<T>(arg: T): T {
  return arg
}
```

핵심은 심플하다. `interface` 도 제네릭을 받을 수 있다는 것이다. 따라서 일반함수처럼 `<Generic Parameter>` 를 넣어주면 된다!

#### Classes

클래스에 적용하는 방법도 매우 간단하다! 그냥 하던것처럼 하면됨...

```typescript
class GenericNumber<NumType> {
  zeroValue: NumType;
  add: (x: NumType, y: NumType) => NumType;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function (x, y) {
  return x + y;
};
```

### Generic Constraints

`Constraints` 에 대해 알아보기 전에 우선 아래의 경우를 보자.

```typescript
function loggingIdentity<Type>(arg: Type): Type {
  console.log(arg.length);
  return arg;
}
```

위의 코드는 에러가 날까? 나지 않을까? 정답은 **에러가 난다.** 그러면 무슨 에러가 날까? 에러 메시지를 보면 `Property 'length' does not exist on type 'Type'.` 이렇다.

즉, `arg` 의 인자가 `length` 를 가지는지를 현재로써는 알 수 없으니깐, 그리고 `length` 프로퍼티가 없는 인자가 들어올 수도 있으니 에러가 난다!! 그러면 아래처럼 하면 안될까?

```typescript
function loggingIdentity<Type>(arg: Type): Type {
  if (!arg.length) {
    console.log("I don't have length!");
    return;
  }
  console.log(arg.length);
  return arg;
}
```

물론 이렇게 해도 괜찮다. 하지만 이건 좋지 않아? **Generic** 을 이용해서 멋지게 만들수있다! 아래를 보자.

```typescript
interface LengthWise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length);
  return arg;
}
```

짜잔~ 이제 우리는 인자로 들어오는 `T` 라는 타입에 `length` 라는 프로퍼티가 항상 있다고 가정한 상태에서 코딩을 할수있게 된다! 그대신 이전처럼 모든 타입의 인자가 들어올 수는 없다. 왜? **제한을 걸었으니깐!**

조금 더 업그레이드해서 이번에는 키 - 밸류 를 갖는 객체에서 키값을 이용해 밸류값을 가져오는 함수를 만들고, 해당 함수에 타입을 넣어보자.

```typescript
// 일반 자바스크립트
function getProperty(obj, key) {
  return obj[key]
}
```

우선 일반 자바스크립트로 구현했다. 이제 타입을 생각해보자. 여기서 확신할 수 있는 한가지는 **key의 value 는 값의 타입은 사용하기 전까지는 알 수 없다는 사실이다.** 즉 우리는 제네릭을 사용해서 일반화해야한다. 자 그럼 이제 일반화해보자.

일반화하려면 무엇을 알아야할까? 간단하다. `keyof` 라는 키워드만 알면된다. `keyof` 는 간단히 객체의 `key` 타입을 가져와주는 키워드다. 첫번째 인자의 타입이 `T` 라면 두번째 인자인 `key` 의 타입은 `key of T` 가 된다!!

```typescript
// keyof 라는 타입을 알았으니...
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");
getProperty(x, "m"); // 에러발생
```

