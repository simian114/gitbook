# NAVBAR 실습

## 1. html 구조를 잡자.

[**html structure**](https://developer.mozilla.org/ko/docs/Learn/HTML/Introduction_to_HTML/Document_and_website_structure) 를 보면 개발자가 지켜야 할 html 의 구조가 있다. 구조를 짠다는 것은 눈에 보이는 곳만 신경을 쓰는게 아닌 앞에서는 보이지 않는, 다른 누군가를 위한 작업이라는 것을 명심하자.

## 2. CSS 에 대해 알게 된 것들..

### a 태그는 기본적으로 텍스트의 색이 다르다.

* `text-decoration` 속성을 통해서 데코레이션을 꾸밀 수 있다. `none` 을 하면 기본 텍스트 색으로 변한다.

### list 의 점은 없앨 수 있다.

* `list-style: none` 을 이용해서 점, 숫자 등을 없애자!!

### padding 과 margin 의 차이?!

* **margin** 을 이용해서 박스의 크기를 키우면 실직적으로 누룰 수 있는 범위으 크기는 늘어나지 않는다.
* **padding** 을 이용해서 박스의 크기를 키우면 누룰 수 있는 범위의 크기가 늘어난다!

### media 을 이용해서 반응형 웹을 반들자!

```css
@media screen and (max-width: 768px) {
  .navbar {
    flex-direction: column;
    align-items: flex-start;
  }
...
}
```

* 브라우저의 크기가 768px 아래로 내려오면 이 블록안에 선언한 css 가 동작한다!

### CSS의 요소를 변수화?!

```css
:root {
  --text-color: #e0f2f1;
  --accent-color: #fb8c00;
  --background-color: #3949ab;
}

.navbar {
  display: flex;
  justify-content: space-between;
  background-color: var(--background-color);
  align-items: center;
  color: var(--text-color);
}
```

## 각종 사이트들을 잘 이용하자

### 아이콘

[**FONT AWESOME**](https://fontawesome.com/)

### COLOR

[**COLOR TOOL**](https://material.io/resources/color/#!/?view.left=0&view.right=0)

### HTML, CSS 레퍼런스

[**MDN WEB DOCS**](https://developer.mozilla.org/ko/)

