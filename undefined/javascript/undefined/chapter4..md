# Chapter4. 제어문

## 조건문

* C랑 같음

## 반복문

* C랑 같음
* 일반적인 반복문은 거의 사용되지 않는다. `for in`, `for of`, `forEach메서드` 등을 사용하자

### for / in 문

> Map 내 원소들을 반복하는 문법

* 해당 객체의 모든 열거할 수 있는 프로퍼티\(enumerable properties\)를 순회할 수 있도록 해준다.
* ```javascript
  for (변수 in 객체) {
      객체의 모든 열거할 수 있는 프로퍼티의 개수만큼 반복적으로 실행하고자 하는 실행문;
  }

  var a = [3,4,5]
  for(var i in a)
    console.log
  // 출력: 0, 1, 2

  // 맵을 for in 으로 반복 돌리면 key가 나온다. value에 접근하기 위해서는 아래처럼
  var obj = { name : "이순신", age : 20 };
  for(var key in obj)
    console.log(key, obj[key])
  ```

### for / of 문

> 배열 내 원소들을 반복하는 문법

* 반복할 수 있는 객체\(iterable object\)를 순회할 수 있도록 해주는 반복문이다.

