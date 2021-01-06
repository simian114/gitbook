# Chapter8. 표준객체

## 전역 객체\(global object\)

* 전역 객체란 JS에 미리 정의된 객체로 전역 프로퍼티나 전역 함수를 담는 공간의 역할을 함
* 전역 객체 그 자체는 전역 범위에서 `this` 연산자를 통해 접근할 수 있다.

## 래퍼 객체\(wrapper object\)

```javascript
var test = "string";
console.log(test.length);
```

* 위의 예시에서 문자열 리터럴 `test`는 객체가 아닌데도 `length` 프로퍼티를 사용하고 있음
* 이게 가능한 이유는 JS에서 `new String(test)`를 호출한 것처럼 문자열 리터럴을 객체로 자동변환해주기 때문이다.
  * 이렇게 생성된 객체는 어디까지나 `임시` 객체다.
  * 따라서 프로퍼티의 참조가 끝나면 임시객체는 자동으로 삭제된다.
* 숫자, 문자열, 불리언 등 원시 타입의 프로퍼티에 접근하려고 할 때 생성되는 임시 객체를 `래퍼 객체`라고 한다.

## Number 객체

* [여기](http://www.tcpschool.com/javascript/js_standard_number)를 보자

### null, undefined, NaN, Infinity에 대한 비교

* `null`: `object` 타입이며, 아직 값이 정해지지 않은 것을 의미
* `undefined`: `null`과는 달리 하나의 타입이며, 아직 `타입`이 정해지지 않은 것을 의미
* `NaN`: `number` 타입이며, `숫자가 아님`을 의미
* `Infinity`: `number` 타입이며 무한대를 의미
* 위의 4가지는 문맥에 따라 자동으로 타입변환이 이루어진다.
  * | 값 | Boolean 문맥 | Number 문맥 | String 문맥 |
    | :---: | :---: | :---: | :---: |
    | null | false | 0 | "null" |
    | undefined | false | NaN | "undefined" |
    | NaN | false | NaN | "NaN" |
    | Infinity | true | Infinity | "Infinity" |

## String

* | 메소드 | 설명 |
  | :---: | :---: |
  | indexOf\(\) | String 인스턴스에서 특정 문자나 문자열이 처음으로 등장하는 위치의 인덱스를 반환함. |
  | lastIndexOf\(\) | String 인스턴스에서 특정 문자나 문자열이 마지막으로 등장하는 위치의 인덱스를 반환함. |
  | charAt\(\) | String 인스턴스에서 전달받은 인덱스에 위치한 문자를 반환함. |
  | charCodeAt\(\) | String 인스턴스에서 전달받은 인덱스에 위치한 문자의 UTF-16 코드를 반환함. \(0 ~ 65535\) |
  | charPointAt\(\) | String 인스턴스에서 전달받은 인덱스에 위치한 문자의 유니코드 코드 포인트\(unicode code point\)를 반환함. |
  | slice\(\) | String 인스턴스에서 전달받은 시작 인덱스부터 종료 인덱스 바로 앞까지의 문자열을 추출한 새 문자열을 반환함. |
  | substring\(\) | String 인스턴스에서 전달받은 시작 인덱스부터 종료 인덱스 바로 앞까지의 문자열을 추출한 새 문자열을 반환함. |
  | substr\(\) | String 인스턴스에서 전달받은 시작 인덱스부터 길이만큼의 문자열을 추출한 새로운 문자열을 반환함. |
  | split\(\) | String 인스턴스에서 구분자\(separator\)를 기준으로 나눈 후, 나뉜 문자열을 하나의 배열로 반환함. |
  | concat\(\) | String 인스턴스에 전달받은 문자열을 결합한 새로운 문자열을 반환함. |
  | toUpperCase\(\) | String 인스턴스의 모든 문자를 대문자로 변환한 새로운 문자열을 반환함. |
  | toLowerCase\(\) | String 인스턴스의 모든 문자를 소문자로 변환한 새로운 문자열을 반환함. |
  | trim\(\) | String 인스턴스의 양 끝에 존재하는 공백과 모든 줄 바꿈 문자\(LF, CR 등\)를 제거한 새로운 문자열을 반환함. |
  | search\(\) | 인수로 전달받은 정규 표현식에 맞는 문자나 문자열이 처음으로 등장하는 위치의 인덱스를 반환함. |
  | replace\(\) | 인수로 전달받은 패턴에 맞는 문자열을 대체 문자열로 변환한 새 문자열을 반환함. |
  | match\(\) | 인수로 전달받은 정규 표현식에 맞는 문자열을 찾아서 하나의 배열로 반환함. |
  | includes\(\) | 인수로 전달받은 문자나 문자열이 포함되어 있는지를 검사한 후 그 결과를 불리언 값으로 반환함. |
  | startsWith\(\) | 인수로 전달받은 문자나 문자열로 시작되는지를 검사한 후 그 결과를 불리언 값으로 반환함. |
  | endsWith\(\) | 인수로 전달받은 문자나 문자열로 끝나는지를 검사한 후 그 결과를 불리언 값으로 반환함. |
  | toLocaleUpperCase\(\) | 영문자뿐만 아니라 모든 언어의 문자를 대문자로 변환한 새로운 문자열을 반환함. |
  | toLocaleLowerCase\(\) | 영문자뿐만 아니라 모든 언어의 문자를 소문자로 변환한 새로운 문자열을 반환함. |
  | localeCompare\(\) | 인수로 전달받은 문자열과 정렬 순서로 비교하여 그 결과를 정수 값으로 반환함. |
  | normalize\(\) | 해당 문자열의 유니코드 표준화 양식\(Unicode Normalization Form\)을 반환함. |
  | repeat\(\) | 해당 문자열을 인수로 전달받은 횟수만큼 반복하여 결합한 새로운 문자열을 반환함. |
  | toString\(\) | String 인스턴스의 값을 문자열로 반환함. |
  | valueOf\(\) | String 인스턴스의 값을 문자열로 반환함. |

