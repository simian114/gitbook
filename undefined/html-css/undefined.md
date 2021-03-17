# 유튜브 화면 만들어보기

{% hint style="info" %}
\*\*\*\*[**드림코딩 엘리**](https://www.youtube.com/watch?v=67stn7Pu7s4&list=PLv2d7VI9OotQ1F92Jp9Ce7ovHEsuRQB3Y&index=15&t=626s)\*\*\*\*

\*\*\*\*[**MDN webkit-line-clamp**](https://developer.mozilla.org/ko/docs/Web/CSS/-webkit-line-clamp)

\*\*\*\*[**box-sizing**](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing)\*\*\*\*

\*\*\*\*[**구글 폰트 사용하는법**](https://www.codingfactory.net/10561)\*\*\*\*

\*\*\*\*[**position - sticky**](https://developer.mozilla.org/en-US/docs/Web/CSS/position)\*\*\*\*

\*\*\*\*[**긴 라인 ... 으로 표현하기**](https://developer.mozilla.org/ko/docs/Web/CSS/-webkit-line-clamp)\*\*\*\*

\*\*\*\*[**margin - auto**](https://developer.mozilla.org/ko/docs/Web/CSS/margin)\*\*\*\*
{% endhint %}

## 모든 요소에 style 적용하기

모든 요소에 패딩과 마진을 0 으로 주고싶다. 또한 **box 내부 컨텐츠 사이즈 + 패딩 마진** 이 **box** 의 사이즈가 되도록 만들어 보자. [**box-size**](https://developer.mozilla.org/ko/docs/Web/CSS/box-sizing)

```css
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
```

## 구글 폰트를 사용해보자

1. [**https://fonts.google.com/**](https://fonts.google.com/) 에 접속하자.
2. 원하는 폰트를 고르고 **select** 하자
   * 이 과정에서 여러 개의 폰트를 선택할 수 있다.
3. 이후 **selected familes** 를 눌러보면 이런 링크가 생긴다. `<link href="https://fonts.googleapis.com/css2?family=**Noto+Sans+KR:wght@100**&family=**Roboto:ital,wght@1,100**&display=swap" rel="stylesheet">`
   * Seleted 된 폰트가 두개인걸 알수있음
4. 이 링크를 html 의 헤드에 복사해 넣자.
5. 이후에는 css에 `font-family: 'Noto Sans'` 처럼 사용할 폰트를 지정해주자.

## 버튼의 기본 속성을 바꾸자

버튼은 기본적으로 **border** 이 있다. 하지만 경계선이 거슬리므로 그냥 지워주자. 또한 마우스를 올렸을 때 포인터의 모양이 바꾸는것까지 해보자.

```css
button,
button:focus {
  /* 겉을 둘러싼느 경계선을 없앤다. */
  border: none;  
  /* 마우스를 대면 포인터의 모양이 손가락으로 변함 */
  cursor: pointer;
  /* 마우스가 올라갔을 때 나오는 테두리 없앰 */
  outline: none;
}
```

## video 속성으로 넣은 비디오의 크기를 어떻게 조절해?

우선 Video 태그가 들어있는 박스의 사이즈에 맞추자. 맞추기 위해서는 **width: 100%** 등을 사용하면 된다.

이렇게 되면 문제가 브라우저의 크기를 매우 크게만들면 html 에 비디오 밖에는 보이지 않게된다. 이 문제를 해결하기 위해서는 **max-witdh** 를 지정하면 됨

```css
.player video {
  width: 100%;
  height: 100%;
  max-width: 1000px;
}
```

## 화면이 아래로 스크롤 도 비디오는 고정시키고 싶어!!

1. **position** 을 **sticky** 로 설정해야 한다.
   * 하지만 이거가지고는 되지 않는다. 왜? **고정 될 위치를 지정하지 않았기 때문**
   * **top**, **right**, **bottom**, **left** 등의 위치를 지정해주는 스타일이 필수다.
   * [**MDN 을 읽어보자.**](https://developer.mozilla.org/en-US/docs/Web/CSS/position)
2. 위치 지정해주기
   * 비디오를 화면위 맨 위에 고정시키고 싶으므로 `top: 0` 스타일을 넣어준다.

```css
.player {
  /* 스크롤링을 해도 플레이어의 위치는 고정! */
  position: sticky; /* 고정 될 위치를 지정해줘야한다! */
  top: 0;
  text-align: center;
  background-color: var(--black-color);
}
```

## 긴 텍스트를 ... 로 표시하고 싶다

이와 관련된 검색어는 [**HTML clamp**](https://developer.mozilla.org/ko/docs/Web/CSS/-webkit-line-clamp) 다. 링크에 들어가면 **-webkit-line-clamp** 를 볼 수 있다.

**-webkit-line-clamp** 속성은 [**블록 컨테이너**](https://developer.mozilla.org/ko/docs/Web/CSS/Containing_block)의 콘텐츠를 지정한 줄 수만큼으로 제한하는 기능을 제공한다.

사용 예를 그대로 사용하면 된다.

```css
.info .metadata .titleAndButton .title.clamp {
  font-size: var(--font-medium);
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  overflow: hidden;
  margin-right: var(--padding);
}
```

## margin auto 를 이용한 자동 중앙정렬?

[**여기 블로그를 보자.**](https://hansolcha.tistory.com/4)

[**MDN도 보자.**](https://developer.mozilla.org/ko/docs/Web/CSS/margin)

## CSS 파일에서 변수의 값을 계산해서는 사용하지 못하나?

**\[MDN\]\(**[https://developer.mozilla.org/ko/docs/Web/CSS/calc\(](https://developer.mozilla.org/ko/docs/Web/CSS/calc%28)**\)\)** 을 보면 있다. 정말 웹 개발은 MDN에 모두 있는거 같다.

실제 사용은 아래처럼 하면 된다.

```css
.info .actions button i {
  /* 아이콘이랑 글씨랑 정령이 안되어 있음 */
  /* 아래처럼 위아래를 auto 로 지정하면 마법처럼 정렬됨 */
  margin: 0 auto;
  /* var(--padding) 은 16인데 이건 너무 크다 */
  /* 반으로 줄이는 방법은 아래처럼 calc 함수를 쓰면됨 */
  margin-bottom: calc(var(--padding) / 2);
  font-size: 16px;
}
```

## 이미지의 기본 크기 때문에 사이즈 조절이 실패한다.

html에서 사용하는 이미지를 사용한다고 하자. 이 때 이미지의 사이즈가 우리가 원하는 사이즈보다 훨씬 크다면, 플렉스 박스를 사용한다고 해도 이미지가 너무 비대해지는 문제가 있다.

이 문제는, 이미지를 **박스에 넣어버리는것!!!**

**div** 태그를 만들어서 **img** 태그를 넣고 flex로 관리하면 된다.

```markup
<div class="img">
    <img src="images/test.jpeg" alt="">
</div>
```

## 하나의 플렉스 박스에 여러 아이템들을 비율크기로 넣어두고 싶어!

하나의 플렉스 박스에 3개의 요소가 있다고해보자. 그리고 박스 전체의 크기가 100 이라고 할 때 각각을 35, 60, 5 로 넣고 싶다면 어떻게 해야할까?

아주 간단하다. 각각의 요소에 **flex** 스타일을 넣어주면 끝

아래를 보면 `flex 1 1 35%` 이런 식으로 되어있는데, 1 1 이 의미하는 바는 가로 세로가 1:1 의 비율로 움직인다는 것을 알려주는 것

```css
.UpNext .item {
  display: flex;
  /* flex: 35 60 5; */
  margin-top: var(--padding);
}

.UpNext .item .img {
  flex: 1 1 35%;
  margin-right: var(--padding);
}

/* 전체에서  설명은 60^*/
.UpNext .item .info {
  flex: 1 1 60%;
}

/* 전체에서 button은 5% */
.UpNext .item .moreBtn {
  flex: 1 1 5%;
}

.UpNext .item .info {
  display: flex;
  flex-direction: column;
}
```

