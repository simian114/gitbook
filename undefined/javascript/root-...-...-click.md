---
description: 자바스크립트의 세계는 정말...
---

# 이벤트가 버블링 되서 root 까지 가다보면... 부모의 부모의 ... 모든 click 이벤트를 발동시키는거 아니야?

{% hint style="info" %}
\*\*\*\*[**W3 UI EVENT**](https://www.w3.org/TR/DOM-Level-3-Events/)\*\*\*\*

\*\*\*\*[**MDN EVENT**](https://developer.mozilla.org/ko/docs/Web/API/Event)\*\*\*\*

\*\*\*\*[**모던 자바스크립트 이벤트 위임**](https://ko.javascript.info/event-delegation)\*\*\*\*
{% endhint %}

## 이벤트가 버블링 되서 root 까지 가다보면... 부모의 부모의 ... 모든 click 이벤트를 발동시키는거 아니야?

```markup
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title> no event delegation</title>
</head>

<body>
  <h1>오늘의 할 일</h1>
  <ul class="itemList">
    <li>
      <input type="checkbox" id="item1">
      <label for="item1">이벤트 버블링 학습</label>
    </li>
    <li>
      <input type="checkbox" id="item2">
      <label for="item2">이벤트 캡쳐 학습</label>
    </li>
  </ul>

  <script>

var itemList = document.querySelector('.itemList');
itemList.addEventListener('click', function(event) {
    console.log('clicked');
  console.log(event);
});
document.body.addEventListener('click', () => {
    console.log('body!!');
})
  </script>
</body>
</html>
```

이런 코드가 있을 때 **li** 요소를 클릭하면 이벤트가 발생하는데 이 이벤트는 버블링 되어서 결국 body 까지 가게 된다. 하지만 나는 body 까지 가지 않는걸 원하는데...?

### 그러면 **stopPropagation** 을 사용하면 되잖아?!

[**모던 자바스크립트 듀토리얼**](https://ko.javascript.info/bubbling-and-capturing) 을 보면 **stopPropagation** 은 왠만하면은 사용하지 않는게 좋다고 한다. 하.. 그럼 도대체 어떻게해?

## event의 target vs currentTarget

이벤트가 발동되면 핸들러에 인자로 **event** 객체가 전달 된다. 이 객체에는 많은 **property** 가 존재하는데 이 중에 target 과 currentTarget 을 알아보자.

이벤트가 발생한 가장 안쪽의 요소는 **target 요소** 라고 불리고 **event.target** 을 사용해 접근할 수 있다.

이벤트가 발생한 가장 안쪽의 요소라는 말이 뭔가 이상한가? 절대 이상하지 않다. 발생한 이벤트가 클릭이라고 한다면, **가장 안쪽의 요소는 클릭 된 요소를 말하는 것이다.** 그리고 그 외의 것들은 이벤트 버블링 때문에 이벤트가 발동한 것.

**event.target** 과 **event.currentTarget\(this\)** 는 다음과 같은 차이점이 존재한다.

* **event.target** 은 실제 이벤트가 시작된 **target 요소이다.** 버블링이 진행되어도 변하지 않는 값이다.
* **event.currentTarget** 는 **현재** 요소로, 현재 실행 중인 핸들러가 할당된 요소를 참조한다. 버블링이 진행되면서 컨텍스트가 변화하면 이 **currentTarget** 또한 변화한다.

## 자, 이제 우리가 선택한 요소 자체에서만 이벤트가 발동하게 하기 위해서 아래처럼 바꾸자.

```markup
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <title> no event delegation</title>
</head>

<body>
  <h1>오늘의 할 일</h1>
  <ul class="itemList">
    <li>
      <input type="checkbox" id="item1">
      <label for="item1">이벤트 버블링 학습</label>
    </li>
    <li>
      <input type="checkbox" id="item2">
      <label for="item2">이벤트 캡쳐 학습</label>
    </li>
  </ul>

  <script>

var itemList = document.querySelector('.itemList');
itemList.addEventListener('click', function(event) {
    console.log('clicked');
  console.log(event);
});
document.body.addEventListener('click', (event) => {
  if (event.target.tagName != 'body') return ;
    console.log('body!!');
})
  </script>
</body>
</html>
```

**body** 요소에 **click 이벤트 핸들러를 약간 수정했다.** 확인해보면 **event.target.tagName** 을 body 와 비교해서 아니라면 이벤트 함수를 실행하지 않게 만든것.

아주 좋아보이는 해결법 같지만 이 해결법은 문제가 있다.

만약, 특정 박스를 누르면 이벤트가 발생한다고 해보자. 그런데 이 박스안에는 다양한 요소가 있고, 그 각 요소를 클릭해도 박스를 클릭하는 것과 같은 동작을 수행해야할 때 위와 같은 방법으로 버블링이 되는걸 막으면 박스 내부 요소를 클릭한다고 해서 원하는 이벤트를 실행하지 못할 것이다.

위의 문제를 막고 싶다면 [**여기를 보자**](https://ko.javascript.info/event-delegation)

