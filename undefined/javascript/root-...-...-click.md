---
description: 자바스크립트의 세계는 정말...
---

# 이벤트가 버블링 되서 root 까지 가다보면... 부모의 부모의 ... 모든 click 이벤트를 발동시키는거 아니야?

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

### target 을 이용해서 버블링을 중간에 의도적으로 멈추자!!!

* [**모던 자바스크립트의 버블링과 캡쳐링**](https://ko.javascript.info/bubbling-and-capturing)
* [**모던자바스크립트의 이벤트 위임**](https://ko.javascript.info/event-delegation)

위 두개 링크를 보면 그 해답을 찾을 수 있다. 내일 꼭 정리하자.

