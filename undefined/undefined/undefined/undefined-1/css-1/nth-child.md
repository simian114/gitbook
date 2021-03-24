# nth-child

{% hint style="info" %}
\*\*\*\*[**nth-child**](https://developer.mozilla.org/ko/docs/Web/CSS/:nth-child%20)\*\*\*\*

\*\*\*\*[**nth-child practice**](https://css-tricks.com/examples/nth-child-tester/)\*\*\*\*
{% endhint %}

## :nth-child

이 [**의사 클래스**](https://developer.mozilla.org/ko/docs/Web/CSS/Pseudo-classes) 는 형제 사이에서의 순서에 따라 요소를 선택한다.

```css
/* 목록의 두 번째 <li> 선택 */
li:nth-child(2) {
  color: lime;
}

/* 임의의 그룹에서 네 번째에 위치하는 모든 요소 선택 */
:nth-child(4n) {
  color: lime;
}
```

## 상황

```markup
<div class="testimonials">
  <div class="testimonial">
    <img
         src="imgs/testimonials/face.jpg"
         alt="face"
         class="testimonial__avatar"
         />
    <div class="testimonial__speech-bubble">
      <p>
        Lorem ipsum dolor sit, amet consectetur adipisicing elit. Ut
        perferendis numquam blanditiis sit reiciendis magni quia hic id
        minima soluta doloribus corporis vitae nemo maiores quae eum,
        asperiores ipsum alias!
      </p>
      <p class="name"><a href="www.naver.com">Ellie</a> / Samsung</p>
    </div>
  </div>
  <div class="testimonial">
    <div class="testimonial__speech-bubble">
      <p>
        Lorem ipsum dolor sit, amet consectetur adipisicing elit. Ut
        perferendis numquam blanditiis sit reiciendis magni quia hic id
        minima soluta doloribus corporis vitae nemo maiores quae eum,
        asperiores ipsum alias!
      </p>
      <p class="name"><a href="www.naver.com">Ellie</a> / Samsung</p>
    </div>
    <img
         src="imgs/testimonials/face.jpg"
         alt="face"
         class="testimonial__avatar"
         />
  </div>
</div>
```

`testimonials` 라는 컨테이너 안에는 `testimonial` 이 두개가 존재한다.

* `testimonials`
  * `testimonial`
  * `testimonial`

여기서 우리는 첫 번째 `testimonial` 과 두 번째 `testimonial` 의 `CSS` 속성을 다르게 주고 싶다.

## 해결

다양한 방법이 있겠지만, `nth-child` 을 이용해보자. `odd` 와 `even` 키워드를 이용해서 스타일링 하자.

```css
.testimonial__avatar:nth-child(odd) {
  margin-right: 40px;
}

.testimonial__avatar:nth-child(even) {
  margin-left: 40px;
}
```

