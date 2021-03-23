# classList

{% hint style="info" %}
\*\*\*\*[**classList**](https://developer.mozilla.org/ko/docs/Web/API/Element/classList%20)\*\*\*\*
{% endhint %}

## 상황

자바스크립트의 **쿼리 셀렉터** 를 이용해서 요소를 찾은 다음에, 특정 조건을 만족하면 **active** 라는 클래스를 추가해주고 싶다. 이럴때는 어떻게할까?

```javascript
const navbar = document.querySelector('#navbar');

if (fullfill condition) {
  # navbar 의 html class 에 active 라는 걸 넣어주고 싶어...
}
```

## 해결

### classList 를 이용하자

**querySelector** 를 이용해 가져올 수 있는 **element** 에는 수많은 메서드가 있는데 그중에 **classList** 라는 속성을 사용하면 된다.

MDN 에 따르면 이 속성은 **DOMTokenList** 를 반환하는 읽기 전용 프로퍼티라고 한다. **classList** 는 공백으로 구분된 문자열인 **element.className** 을 통해 클래스 목록에 접근한다고 한다.

즉, **className** 을 사용하면 `<div class="test1 test2 test3">` 에서 `test1 test2 test3` 이 그대로 얻어와지는거고 **classList** 를 사용하면 이 문자열이 공백을 기준으로 split 되서 배열의 형태로 오게 되는 것이다.

**classList** 에서 제공하면 몇 가지 메서드를 사용하면 이 클래스를 동적으로 **추가, 제거, 변경** 할 수 있다. 그리고 **존재 여부 확인**까지 가능하다.

```javascript
const navbar = document.querySelector('#navbar');

if (fullfill condition) {
  navbar.classList.add('') # 추가
  navbar.classList.remove('') # 제거
  navbar.classList.replace('test1', 'test2') # test1 을 test2 로 변경
  navbar.classList.contains('test1') # test1이 있는지 검사. true, false 리턴
}
```

