# Chapter7-2. 객체

## 객체 생성자

* 일반 함수처럼 `function`  키워드를 사용한다.
* 일반 함수와 달리 `대문자`로 시작하게 만든다. 이게 규칙이다.
* 이후에는 `new` 연산자를 호출해서 인스턴스를 생성할 수 있다.
* 생성자 안에서 `this`를 사용함으로써 this에 저장된 것들이 객체가 생성될 때 그 객체에 적용된다.

## 프로토타입

* 아래와 같은 생성자가 있을 때 무슨 문제가 있을지 생각해보자.

```javascript
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.eat = function(menu) {
    console.log(this.name + " eats " + menu);
  }
}
```

* 문제는 바로 인스턴스가 생길 때마다 `eat` 함수가 계속 정의 된다는 것.
* 사실 `eat` 함수는 각 객체마다 생길 필요는 없고 어딘가에 하나만 존재하고, 객체는 그 함수에 연결선만 가지고 있으면 된다.
  * 위와 같은 생각을 실현해주는게 바로 `prototype` 메서드다.
* ```javascript
  function Person(name, age) {
    this.name = name;
    this.age = age;
  }

  Person.prototype.eat = function(menu) {
    console.log(this.name + " eats " + menu);
  }
  ```
* `prototype` 객체는 `원형`을 뜻한다.
* _**같은 생성자로부터 만들어진 객체들은 모두 이 원형 객체를 공유한다.**_
* 이제 `new Person`으로 만들어진 모든 객체들은 `eat`이라는 메서드를 사용할 수 있다.

### 정리

* 생성자 내부의 `this`에 메서드를 넣어주면 객체 하나를 만들때마다 메서드도 하나씩 만들어준다.
  * 매우 심각한 메모리 낭비 문제가 발생할 수 있다.
* 이 문제를 해결해 주는게 바로 `prototype`이다.
  * `prototype`는 모든 객체가 공유하고 있어서 한 번만 만들어지기 때문에 메모리 낭비에서 자유로울 수 있다.
* 메서드 뿐만 아니라 속성까지 모두 `prototype`에 넣을 수 있다.

## `Prototype`과 `__proto__`

* \[간단한 설명\]\([https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67](https://medium.com/@bluesh55/javascript-prototype-이해하기-f8e67c286b67)\)
* [깊은 설명](http://insanehong.kr/post/javascript-prototype/)
* 자바스크립트는 프로토타입 기반의 객체지향 언어다.
* JS는 함수를 통해 프로토타입을 만든다.
* 함수가 정의될 때 2가지 일이 동시에 이뤄진다.
  1. 해당 함수에 `Constructor(생성자)` 자격 부여
  2. 해당 함수의 `Prototype Object 생성 및 연결`
* 함수를 정의하면 함수만 생성되는 것이 아닌 `Prototype Object`도 같이 생성된다.
  * 함수에서 `Prototype Object`에 접근하기 위해선s `Person.prototype` 처럼 `prototype` 속성으로 접근한다.
* `Prototype Object`는 일반적인 객체와 같으며 속성으로는 아래 두가지를 갖는다.
  * `constructor`
  * `__proto__`
* `constructor`는 생성자 자신을 가리킨다.
* `__proto__`는 `Prototype Link`다.
  * `constructor`로 생성된 객체\(인스턴스\)는 속성으로 `__proto__`을 갖는다.
    * 일반 객체는 `prototype` 속성을 갖지 않는다.
  * `__proto__`는 객체가 생성될 때 조상이었던 함수의 `Prototype Object`를 가리킨다.

