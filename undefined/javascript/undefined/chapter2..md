# Chapter2. 타입

## 기본 타입

* 자바스크립트에서는 여러 가지 형태의 타입을 미리 정의하여 제공하고 있다. 이것을 기본타입 이라고 한다.
* 기본타입은 크게 아래처럼 나뉜다.
  * 원시 타입
    * 숫자
    * 심볼
    * 문자열
    * 불리언
    * undefined
  * 객체 타입
    * 객체

### 숫자

* 정수와 실수를 구분하지 않는다.
  * 모든 수를 `실수` 하나로만 표현한다.
  * 매우 큰 수나 매우 작은 수를 표현하는 경우에는 `e 표기법` 을 사용할 수 있다.
* ```javascript
  var firstNum = 10;     // 소수점을 사용하지 않은 표현
  var secondNum = 10.00; // 소수점을 사용한 표현
  var thirdNum = 10e6;   // 10000000
  var fourthNum = 10e-6; // 0.00001
  ```

### 문자열

* 큰 따옴표나 작은 따옴표로 둘러싸인 문자의 집합
* 문자열을 더할 수도 있다. `+` 연산자를 사용하면 된다.
* 문자열이 아닌 숫자와도 문자열을 더할 수 있다.
* ```javascript
  var num = 10;
  var str = "JavaScript";
  document.getElementById("result").innerHTML = (num + str); // 10JavaScript
  ```

### 불리언

* 예약어로 `true`와 `false`가 있다.

### 심볼

* 심볼 타입은 _**유일하고 변경할 수 없는 타입으로, 객체의 프로퍼티를 위한 식별자로 사용할 수 있다.**_

### typeof 연산자

* 피연산자의 타입을 변환한다.
* ```javascript
  typeof 10;        // number 타입
  typeof "문자열";  // string 타입
  typeof true;      // boolean 타입
  typeof undefined; // undefined 타입
  typeof null;      // object 타입
  ```

### null과 undefined

* `null` 이란 `object` 타입이며 아직 값이 정해지지 않은 것을 의미
* `undefined` 란 `타입`이 정해지지 않은 것을 의미
* ```javascript
  var num;          // 초기화하지 않았으므로 undefined 값을 반환함.
  var str = null;   // object 타입의 null 값
  typeof secondNum; // 정의되지 않은 변수에 접근하면 undefined 값을 반환함.
  ```
* null과 undefined는 동등연산자\(==\) 와 일치 연산자\(===\)로 비굫ㄹ 때 그 결과값이 다르다.
* ```javascript
  null ==  undefined; // true
  null === undefined; // false
  ```

### 객체

* 자바스크립트의 기본 타입은 객체다.

## 타입변환

### 타입변환

* 자바스크립트는 매우 유연한 언어다.
* 변수는 타입이 정해져 있지 않기 때문에 같은 변수에 다른 타입의 값을 대입할 수 있다.

### 묵시적 타입 변환\(implicit type conversion\)

* 특정 타입의 값을 기대하는 곳에 다른 타입의 값이 오면, 자동으로 타입을 변환하여 사용한다.
  * 문자열 + 숫자를 하면 숫자가 자동으로 문자열로 변환된다.

### 명시적 타입 변환\(explicit type conversion\)

* 자스 에서는 묵시적 타입 변환을 많이 사용하지만, 명시적으로 타입을 변환할 방법도 제공함.
* 자스에서 제공하는 타입 변환 전역 함수는 아래와 같다. \(메서드가 아니다. 전역함수다!\)
  1. Number\(\)
  2. String\(\)
  3. Boolean\(\)
  4. Object\(\)
  5. parseInt\(\)
  6. parseFloat\(\)

### 숫자를 문자열로 변환

1. 전역 함수
   * `String()`
2. 메서드
   * `toString()` : null, undefined를 제외한 모든 타입이 갖고 있다.
3. ```javascript
   String(true);     // 문자열 "true"
   false.toString(); // 문자열 "false"
   ```

## 변수

### 변수의 선언과 초기화

* `var` 키워드를 사용하여 변수를 선언한다
* 선언되지 않은 변수를 사용하려고 하거나 접근하려고 하면 오류가 발생한다.
* 단, 선언되지 않은 변수를 초기화할 경우에는 자동으로 선언을 먼저 한 후 초기화를 진행한다.
* ```javascript
  var month; // month라는 이름의 변수 선언
  date = 25; // date라는 이름의 변수를 묵시적으로 선언
  ```
* 쉼표 연산자를 이용하여 여러 변수를 동시에 선언하거나 초기화할 수 있다.

### 변수의 타입과 초깃값

* 같은 변수에 여러 타입의 값을 재대입할수있음
* 그러나 한번 선언된 변수를 재선언할수는 없다.

