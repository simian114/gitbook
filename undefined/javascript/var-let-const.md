# var, let, const 차이



{% embed url="https://curryyou.tistory.com/192" caption="참고 블로그" %}

{% embed url="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/var" caption="var와 호이스팅" %}

{% embed url="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let" caption="let 과 TDZ" %}

## var 에 대해 알아보자

> 자바스크립트를 처음 사용할 때 부터 `var` 의 존재는 익히 들어 알고있었지만, 사용해본적은 없었다. 이참에 알아보자.

### 변수 선언 및 초기화

자바스크립트에서 변수 선언은 선언 + 초기화 로 이루어진다. 간단히 말해보자면

* 선언: 자바스크립트엥게 변수의 존재를 알린다.
* 초기화: 확보된 메모리 공간에 값을 넣는다. 만약 값이 없다면 `undefined` 가 된다.

여기까지는 다른 언어와 동일하지만, 자바스크립트에는 `호이스팅` 이라는 괴상한게 존재한다.

```javascript
console.log(name);  // undefined
var name = 'sanam';
console.log(name); // sanam
```

위와같은 코드가 있다면 분명 에러가 나야하겠지만, 자바스크립트에서는 `undefined` 가 출력될 뿐이다. 왜 그럴까? 왜냐하면 자바스크립트의 `호이스팅` 은 **코드 실행 이전에 모든 선언문\(변수 선언문, 함수 선언문 등..\)등을 찾아내서 먼저 실행하기 때문이다.** 따라서 변수 선언이 어디에 있던 상관없이 다른 코드보다 먼저 실행되게 된다.

> **`var`, `let,` `const`, `function`, `class` 등 의 키워드로 선언된 모든 식별자들은 호이스팅 된다.** `선언` 만 먼저 되고 `할당`은 런타임에서 이루어진다.

위의 코드를 분석해보면 아래와 같다.

```javascript
var name;                // 선언문은 호이시팅 되서 위로 올라옴. 단 아직 할당은 되지 않음.
consoel.log(name)        // undefined
name = 'sanam';          // 할당
consoel.log(name)        // sanam
```

#### 궁금증

> 함수선언문과 함수표현식에 대해 학습하자.

위에서 변수 선언문과 함수 선언문이 호이스팅이 되어서 맨 위에 오게된다는걸 알게되었다. 그런데 아래 코드에서 왜 함수는 선언 이전에 잘 실행이 되는데 `var` 로 선언한 변수는 `undefined` 가 될까?

```javascript
func() // '이건 잘 된다'
console.log(value) // undefined

var value = 10;
function func() {
    console.log('이건 잘 된다');
}
```

그 이유는 **함수 `function func() { ..code .. }` 자체가 함수선언문이기 때문이다.** 따라서 위의 코드를 자바스크립트가 되어서 생각해본다면 아래처럼 될 것이다.

```javascript
var value;
function func() {
    console.log('이건 잘 된다');
}
func();
console.log(value);
value = 10;
```

### 스코프

> 스코프는 식별자들의 유효범위를 의미한다. 선언된 위치에 따라 유효범위가 달라진다.

전역 변수는 어디에서든지 참조가 가능하다. 지역변수의 유효범위는 현재 자신의 스코프와 그 하위 스코프에서 유효하다. `C` 언어와 `C++` 를 생각해보면 각 변수는 블록레벨에 스코프였다. 하지만 `var` 는 블록레벨이 아닌 **함수레벨스코프** 다. 아래의 예시를 보자.

```javascript
for (var i = 10; i < 10; I++);
console.log(i);    // 10
```

다른 언어 같았으면 `i` 의 출력 결과는 `not defined` 같이 나와야겠지만, 자바스키립트는 **함수레벨스코프** 를 따르고 `for` 반복문은 함수레벨이 아니기 때문엔 `var`의 유효범위가 같게 되어서 10이 출력된다.

아래의 경우는 변수 `a` 는 `test` 라는 함수범위 안에서 유효하므로 에러가 발생한다.

```javascript
console.log(a);
function test() {
    var a = 10;
}
```

## 이제 var와 let, const 의 차이점을 알아보자.

크게 5가지 차이점이 존재한다.

1. 중복선언 가능 여부
2. 재할당 가능 여부
3. 변수 스코프 유효범위
4. 변수 호이스팅 방식
5. 전역객체 프로퍼티 여부

### 1. 중복선언

#### var 는 중복선언이 가능하다.

```javascript
// NOTE: 선언 및 초기화
var name = 'sanam';
console.log(name); // sanam

// NOTE: 선언 및 초기화
var name = 'nam';
console.log(name);  // nam

// NOTE: 선언
var name;
console.log(name); // nam
```

`var` 로 선언한 변수는 중복해서 선언 및 초기화가 가능하다. 만약 초기화를 하지 않고 **선언** 만 할 경우 마지막에 할당된 값이 변수에 저장된다. 아래의 문제를 풀어보자. 각 `console.log` 에는 어떤 값이 찍힐까?

```javascript
function quiz(x) {
    console.log(x);

    var x;
    console.log(x);

    var x = 2;
    console.log(x);
}
quiz(1);
```

정답은 .... `1 1 2`다! 이유를 생각해보면.... 1. `quiz` 라는 함수의 인자 `x` 에 1을 넣었다. 2. 첫번쨰 로그에서는 인자로 들어온 1이 출력된다. 3. `var x` 로 변수를 선언했다. 선언만 하고 초기화는 하지 않았으므로 인자로 이전의 값인 1이 들어온다. 4. 두번째 출력도 1이 된다. 5. `var x = 2` 로 선언 및 초기화가 이루어졌다. 따라서 값이 변했으므로 출력 또한 2로 변하게 된다.

> 이 경우에 `let x` 를 하면 당연히 안된다!

#### let, const 는 중복선언이 불가능하다.

```javascript
const test1 = 10;
const test1 = 10; // Identifier 'test1' has already been declared

let test2 = 10;
let test2 = 10; // Identifier 'test2' has already been declared
```

중복선언이 불가능하다.

### 2. 재할당

> var와 let은 재할당이 가능하다. const 는 그러면?

#### const 는 재할당이 불가능하다.

재할당이 불가능하다. 재할당이 불가능하다는 말을 풀어 말하면 선언과 동시에 할당이 되지 않으면 에러가 발생한다는 것. 따라서 초기값을 항상줘야한다.

```javascript
const test;    // Uncaught SyntaxError: Missing initializer in const declaration
```

만약 재할당을 하게 되면 에러가 발생한다.

```javascript
const name = 'sanam';
name = 'nam'; // Uncaught TypeError: Assignment to constant variable.
```

### 3. 스코프

> 앞서 var는 **함수레벨스코프** 를 따른다고 했다. **let과 const 는 블록레벨 스코프를 따른다!**

블록레벨 스코프는 함수 내부, `if문`, `for` 문 그리고 단순한 코드 블럭 `{ }` 에서 선언된 변수를 지역변수로 취급한다.

```javascript
const name = 'sanam';

if (true) {
    const name = 'if';
    console.log(name);    // if
}

for(i = 0;i < 1;i++) {
    const name = 'for';
    console.log(name);    // for
}

{
    const name = 'block';
    console.log(name);    // block
}

{
     console.log(name);    // sanam
}

마지막 블록 출력값은 `sanam` 으로, 블록밖의 변수가 참조되었다. 왜? 본인의 스코프에서 찾지 못하면 상위의 스코프에 있는 값을 찾아오기 때문이다.
```

### 4. 호이스팅

`var` 을 이야기할 때 자바스크립트는 선언문을 대상으로 호이스팅을 한다고 이야기했다. `var`변수선언의 호이스팅으로 인해 선언문 자체만 위로 끌어올려진다. 그리고 `undefined` 값이 할당이 된다. 따라서 변수의 값을 넣어 주기 전까지는 해당 변수는 `undefined` 의 값을 갖게된다. **그러면 let 도 var 처럼 재할당이 가능한 키워드니깐, 호이스팅이 되어서 초기화를 해주기 전까지는 `undefined` 가 되겠군!!** 이라고 생각하면 큰 오산이다!!!

#### var와 let, const 의 호이스팅은 다른 방식으로 이루어진다.

> TDZ \(Temporal Dead Zone\): 변수의 선언\(호이스팅\) 과 초기화 사이에 존재하는 일시적으로 변수 값을 참조할 수 없는 구간

이전의 추측대로 `let` 을 사용해보자. `var` 와는 다르게 초기화를 하기 전에는 접근할 수 없다는 에러 메시지가 나온다.

```javascript
console.log(a);    // Uncaught ReferenceError: Cannot access 'a' before initialization
let a;
```

도대체 `let` 과 `const` 는 어떻게 생겨먹었길래 `var` 와 다르게 호이스팅이 될까? 간단하다. `var` 는 호이스팅이 되고 `undefined` 값이 세팅이 되지만 `let`과 `const` 는 초기화과정이 없는 것이다.

**즉, 변수의 선언\(호이스팅\) 과 초기화 사이에 일시적으로 변수 값을 참조할 수 없는 구간이 생기는 것. 이 구간을 TDZ\(Temporal Dead Zone\) 이라고 한다.**

#### TDZ를 어떻게 확인할 수 있을까?

> 간단한 예시를 들어보자.

```javascript
let test = 10;
{
    console.log(test);    // Uncaught ReferenceError: Cannot access 'a' before initialization
    let test = 20; 
}
```

만약 아래의 `let test = 20` 라인이 없다면 콘솔로그는 `10` 이 나와야한다.. 하지만 초기화전에는 접근할 수 없다는 에러메시지가 나온다. 왜? 왜냐면 블록스코프에 존재하는 `test` 라는 변수가 호이스팅 되서 위에서 선언되었기 때문이다. 따라서 상위 스코프에 있는 `test` 가 무시되고 하위 스코프의 `test` 를 참조하게 되는데, 로그 이후에 초기화가 되므로 이런 에러가 나오는 것.

#### TDZ를 조금 더 살펴보자.

> [MDN을 보자](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

아래의 예시는 잘 동작한다. `func` 함수에서 사용되는 `letVar` 변수가 함수표현식 이후에 선언 및 초기화 되고 있지만, 선언 자체는 호이스팅으로 문제 없이 되고, 함수의 호출은 `letVar`의 `TDZ` 이후에 일어나기 때문이다!

```javascript
// letVar TDZ starts
const func = () => console.log(letVar);

let letVar = 3;
// letVar TDZ ends
func();
```

아래의 코드에서는 왜 에러가 발생했을까? 그리고 어떻게 `if` 문 안으로 들어간 걸까?

* if의 foo는 `var foo` 다. 따라서 `if`  블록 안으로 들어가게 된다.
* 블록스코프에서 `foo` 가 선언되었다. 따라서 상위 스코프의 `foo` 는 무시되고 블록에서 선언된 `foo` 를 사용하게된다.
* 여기서 문제가 발생한다. 지금 `foo` 는 선언및 초기화 과정에서 자기 자신의 값을 사용하려고한다. 하지만 우리는 실행 순서가 무엇인지 알고 있다. 할당 연산자를 사용할 때는 2번째 항이 먼저 계산된다. 즉 `foo` 는 초기화를 하기도 전에 자기 자신을 참조하고 있는것. 따라서 에러가 발생함

```javascript
function test(){
   var foo = 33;
   if(foo) {
      let foo = (foo + 55); // ReferenceError
   }
}
test();
```

### 5. 전역객체 프로퍼티 여부

#### var 로 선언된 변수는 window 의 프로퍼티가 된다.

```javascript
var name = 'sanam`;
console.log(window.name);
```

#### let, const 로 선언된 변수는 전역객체의 프로퍼티가 되지 않는다.

```javascript
let name = 'sanam`;
console.log(window.name);    // undefined
```

