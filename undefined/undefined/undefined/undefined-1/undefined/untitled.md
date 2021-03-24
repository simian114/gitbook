# 2. navbar 버튼을 누르면 해당 페이지로 스크롤링 되게 만들자

{% hint style="info" %}
\*\*\*\*[**ScrollIntoView 요소로 바로 이동!**](https://developer.mozilla.org/ko/docs/Web/API/Element/scrollIntoView%20)\*\*\*\*

\*\*\*\*[**스크롤 시 부드러운 이펙트 주**](https://www.w3schools.com/howto/howto_css_smooth_scroll.asp)
{% endhint %}

## 상황

**navbar** 메뉴의 버튼을 눌렀을 때 아무런 동작도 없다. 버튼을 누르면 해당 요소로 스크롤 되게 만들어보자.

### 알아볼 것

1. 특정 DOM 요소로 바로 이동할 수 있는 방법
2. 스크롤 이벤트를 부드럽게 하는 방법

## 해결

```javascript
const navbarMenu = document.querySelector('.navbar__menu');
navbarMenu.addEventListener('click', (event) => {
  const targetLink = event.target.dataset.link
  if (targetLink == null) {
    return;
  }
  scrollIntoView(targetLink);
});
```

`navbar__menu` 에 이벤트를 걸면 자동으로 **이벤트위임** 이 동작해서 해당 리스트의 메뉴들에도 **클릭이벤트를 등록시킬 수 있다.**

눈여겨 볼건 `data` 의 활용이다. 현재 우리가 이동해야할 섹션은 고유의 ID 가 있기 때문에 해당 ID 를 이용해서 요소를 찾으면 되는데, 문제는 `navbar__menu` 의 버튼을 눌렀을 때 그 **고유의 ID** 를 가져올 수가 없다는 것. 따라서 고유의 ID 를 `navbar__menu__item` 에 `data` 값 으로 넣어두고 활용하는 방식으로 문제를 해결했다.

### 예상치 못한 문제

해당 요소가 아니라 **navbar\_\_menu** 자체\(`ul 태그`\)를 클릭할 때는 **undefined 동작** 이 발생한다는 것. 따라서 만약 클릭한 요소가 **navbar\_\_menu** 자체라면 여기서는 바로 리턴해서 앞으로 발생할 수 있는 일을 미연에 방지하자.

