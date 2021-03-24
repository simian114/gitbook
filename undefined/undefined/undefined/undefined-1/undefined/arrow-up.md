# 스크롤 다운 하면 arrow-up 버튼 나오게 하기

{% hint style="info" %}
\*\*\*\*[**display: none 에는 transition이 적용되지 않는다.**](https://velog.io/@dev-tinkerbell/display-none%EC%9D%B4-transition%EC%9D%B4-%EC%95%88%EB%A8%B9%ED%9E%88%EB%8A%94-%EC%9D%B4%EC%9C%A0)\*\*\*\*

\*\*\*\*[**css - animation 을 활용해서 display: none 에도 transition 을 적용**](https://blog.hyungsub.com/entry/display-none%EC%97%90-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0)\*\*\*\*

\*\*\*\*[**visibility**](https://developer.mozilla.org/ko/docs/Web/CSS/visibility)

\*\*\*\*[**pointer-events**](https://developer.mozilla.org/ko/docs/Web/CSS/pointer-events) **요소의 마우스 포인터 클릭 이벤트 없애**
{% endhint %}

## 상황

우리가 만든 페이지는 단 하나의 페이지이고, 아래로 페이지를 내리면 내릴수록 위로 올라가는게 힘들어진다. 이런 경우를 위해서 아래로 어느정도 스크롤이 되면 **특정 버튼이 나타나고 이 버튼을 누르면 페이지의 맨 위로 올리는 작업을 하고싶다.**

### 할 것

1. index.html 에 버튼 만들기
2. css 작업하기
   * 보일 때와 보이지 않을 때 두 가지 경우를 처리해야한다.
   * 보이지 않는 경우를 어떻게 만들 것인가?
3. 특정 조건이 만족되면 메서드를 호출해서 버튼을 보여주자.

## 해결

## html

font-awesome 에 가서 화살표 하나 가져오고 해당 버튼을 index.html에 추가해준다.

```markup
<button class="arrow-up">
  <i class="fas fa-arrow-up"></i>
</button>
```

## javascript

이제 JS 에서 이벤트를 등록해주자.

```javascript
const arrowUpBtn = document.querySelector('.arrow-up');
document.addEventListener('scroll', () => {
  if (window.scrollY < homeHeight / 2) {
    arrowUpBtn.classList.remove('visible');
    return;
  }
  arrowUpBtn.classList.add('visible');
});
arrowUpBtn.addEventListener('click', () => {
  scrollIntoView('#home');
})
```

## 여기의 하이라이트는 CSS 다!!!

```css
.arrow-up {
  position: fixed;
  font-size: var(--font-large);
  color: var(--color-white);
  border-radius: 50%;
  background-color: var(--color-pink);
  top: 100vh;
  left: 100vw;
  cursor: pointer;
  /* 다만 opacity를 0으로 만들면 문제가 보이지 않는 상황에서도 버튼을 누룰 수가 있다.*/
  /* 이에대한 해결책으로는 몇가지가 있는데, 버튼이 안보일 때는 애초에 현재 페이지에서 보이지 않게 만들거나 */

  /* opacity: 0; */
  visibility: hidden;
/* pointer-events 을 none 으로 만들면 된다. */
  pointer-events: none;
  transition: all var(--animation-duration) ease; 
}

.arrow-up.visible {
/* opacity: 1; */
  visibility: visible;
  pointer-events: auto;
  top: 87vh;
  left: 91vw;
}
```

### display: none은 transition 을 적용하지 못한다.\(적용할수있는 방법이 있다!\)

> transition을 적용하지 못한다.

이전에 요소가 특정 조건에 따라 보이거나 보이지 않게 만들 때는 보통 `display: none, block, inline` 을 이용해서 구현했다. 하지만 이렇게하면 문제가 되는게 **transition** 이 적용되지 않는다. 적용되지 않는 이유는 [**여기의 링크**](https://velog.io/@dev-tinkerbell/display-none%EC%9D%B4-transition%EC%9D%B4-%EC%95%88%EB%A8%B9%ED%9E%88%EB%8A%94-%EC%9D%B4%EC%9C%A0) 를 보자. 요약하면 `display:none` **이 적용되면 노드가 렌더링 트리에서 생략되기 때문에 아예 적용이 되지 못하는것.**

### opacity 를 이용하자

Opacity 는 투명도를 조절하는 것. 따라서 이 방법을 이용하면 쉽게 구현할 수 있다. 다만 이 방법은 **투명 상태에서도 사용자가 누를 수 있다!!** 라는 치명적인 문제점을 갖고 있다.

이 문제을 해결하는 방법은 두 가지가 있다.

1. 투명인 상태에서는 아예 **뷰포트 밖에 만들어두자!**
   * 그리고 조건이 성립하면 내부로 이동시키는것
2. 해당 요소의 클릭 이벤트를 없애는 것
   * `pointer-event: none` 으로 하면됨

### opacity 말고 visibility 를 사용하자

`visibility` 를 사용하면 위의 문제점 없이 바로 사용할 수 있다!!!

