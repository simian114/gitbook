# prototype, \[\[Prototype\]\] 차이

## prototype 과 \[\[Prototype\]\]

자바스크립트 함수는 객체다.

함수 역시 일반적인 객체의 기능에 추가로 호출됐을 때 정의된 코드를 실행하는 기능을 가지고 있다는 것이다.

일반 객체와는 다르게 추가로 **함수 객체만의 표준 프로퍼티가 정의되어 있다.** 아래와 같은 함수가 있다고 해보자.

```javascript
function add(x, y,) {
  return x + y;
}
console.dir(add);

// 더불어서 일반 객체 또한 찍어보자
let normal = {
  name: "normal",
  age: 20,
}
console.dir(normal)
```

다른건 제두고 **protorype** 만 살펴보자. 자 어떤 차이가 존재하는가?

#### 일반객체

```text
age: 20
name: "a"
<prototype>: Object { … }
```

#### 함수

```text
arguments: null
caller: null
length: 2
name: "add"
prototype: Object { … }
<prototype>: function ()
```

함수에는 일반객체에는 존재하지 않는 **protorype** 프로퍼티, name, caller, arguments 등이 존재한다.

### `<prototype>`== \[\[Prototype\]\] == `__proto__`

모든 자바스크립트 객체는 자신의 프로토타입을 가리키는 \[\[Prototype\]\] 을 가지고 있다.

따라서 일반 객체와 함수 이 둘도 당연히 자기 자신을 가리키는 프로토타입인 **\[\[Prototype\]\]** 이 존재하는 것.

자, 그럼 **함수에만 존재하는 prototype** 은 대체 무엇인가? 이 비밀을 풀기 위해서는 **생성자 함수를 알아보면 된다.**

### 생성자 함수

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
}
sanam = new Person("sanam", 28)

console.dir(sanam)
console.dir(Person)
```

#### 생성자 함수

```text
arguments: null
caller: null
length: 1
name: "Person"
prototype: {…}
    constructor: function Person(name)
    <prototype>: Object { … }
<prototype>: ()
```

#### 생성된 객체

```text
name: "sanam"
<prototype>: {…}
    constructor: function Person(name)
    <prototype>: Object { … }
```

이 둘을 분석하기에 앞서 둘 모두에 존재하는 `<prorotype>` 을 살펴보자.

위에서 말했다시피 `<prototype>` **자기 자신의 프로토 타입을 가리킨다.**

한번 예상해보자. _**프로토타입이 자기 자신의 프로토타입을 가리킨다면, 생성자 함수로 생성된 객체의 프로토타입은 생성자 함수인건가?**_ 아주 합리적인 의심이라고 생각한다. _**그리고 이 개념은 절반은 맞고 절반은 틀리다!!!**_

### 왜 절반은 맞고 절반은 틀려?

**왜냐하면, 실제로 생성자 함수를 가리키는 건 맞지만 생성자 함수의 특정한 어떤 것을 가리키고 있기 때문이다.**

생성자 함수의 `console.dir(Person)` 을 다시보자. 일반 객체에는 존재하지 않던 프로퍼티 **prototype** 이 존재하는 걸 확인할 수 있다. **prototype** 프로퍼티를 자세히 보자. 생성된 객체의 `<prototype>` 과 뭔가 닮지 않았나?

그렇다. 생성자 함수의 프로퍼티 prototype 과 생성된 객체의 `<prototype>` 은 같다!!!

```javascript
// 출력 결과는 SAME!!!
if (sanam.__proto__ == Person.prototype) {
    console.log("SAME!!!")
}
```

### 정리

**자 이제 정리하자. 생성자 함수에 존재하는 `<prototype>` 은 생성자 함수의 프로토타입이다. 그리고 생성자함수의 프로퍼티 prototype 은 자신으로 인해 생성되는 객체의 프로토타입다!!**

책에 정리된 내용을 적어보면 이렇다.

> 모든 객체에 내부 프로퍼티로 존재하는 \[\[Prototype\]\] 은 객체 입장에서 자신의 부모 역할을 하는 프로토타입 객체를 가리키는 반면에, 함수 객체가 가지는 prototype 프로퍼티는 이 함수가 생성자로 사용될 때 이 함수를 통해 생성된 객체의 부모 역할을 하는 프로토타입 객체를 가리킨다.

