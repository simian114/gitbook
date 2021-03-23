# CSS 팁

## box sizing

[https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)

css의 `box-sizing`

* content-box: border, padding 을 포함하지 않은 크기. 기본 디폴트
* border-box: border, padding 을 포함해서 박스 크기를 정함. 대부분 이렇게 한다

## CSS Position

> Static, relative, sticky 는 해당 요소가 들어있는 박스에서 상대적인 위치를 구한다.
>
> absolute 는 들어있는 근접한 부모 박스들 중에 static 이 아닌 부모의 위치를 기준으로 한다.
>
> fixed는 들어있는 박스와 상관없이, viewport 를 기준으로 포지션 변경이 일어난다.

### static

* top, left, right 등등이 먹히지 않는다.
* static 은 디폴트 옵션이다.
* static 이 사용된 요소는 현재 자신이 들어있는 **박스에서 자신의 위치를 고수한다.**
* 위치 지정은 top, left, right, bottom 을 이용한다

### absolute

* absolute 를 사용하면 아이템이 담겨있는 박스를 기준으로 지정된 만큼 움직인다.
  * **기준이 되는 상위 박스는 근접한 박스중에 static 이 아닌 박스다.**
* Absolute 가 지정되면 위치를 고수하지 못한다.
* 박스안의 다른 요소들까지 재배치를 하게 된다.

### relative

* 위치를 고수하면서 위치를 수정한다.
* 박스 안의 다른 요소들의 재배치가 일어나지 않는다.
* 위치 지정은 top, left, right, bottom 을 이용한다

```css
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <!-- https://developer.mozilla.org/en-US/docs/Web/CSS/position -->
    <!-- https://developer.mozilla.org/en-US/docs/Web/CSS/Containing_Block -->
    <style>
      .outer {
        width: 200px;
        height: 200px;
        margin-bottom: 20px;
        background-color: green;
      }
      .box {
        width: 100px;
        height: 100px;
        background-color: red;
        position: relative;
      }
      .inner {
        width: 100%;
        height: 50%;
      }
      .inner1 {
        background-color: blue;
      }
      .inner2 {
        background-color: yellow;
      }

      .box1 .inner1 {
        /* position: static 을 사용하면 top left 등은 아무런 영향이 없음 */
        /* 왜냐하면 있어야 할 곳에 그대로 있는거기 때문 */
        /* relative 를 사용하면 요소가 원래 있어야 할 곳은 지킨다. */
        /* 이 말이 뭐냐하면 아래으 absolute 를 사용하면 이 요소 자체가 박스에서 사라진걸로 간주하고 */
        /* 다른 요소가 이 요소를 차지하는데, relavtive 를 사용하면 요소 자체는 현재 박스에 남아있는걸로 치고 */
        /* 위치만 바꾸는거임 */
        /* inner2 는 아무런 변동이 없다. */
        position: relative;
        top: 30px;
        left: 100px;
      }

      .box2 .inner1 {
        /* 기존의 inner2 도 위치가 변했다? */
        /* 왜냐하면 absolute 를 사용하면 요소가 원래 있어야 할 곳에서 빠져버리기 때문에 */
        /* 즉, inner1 아이템이 사라졌기 때문에 inner2 박스가 inner1 박스의 위치로 오게된 것*/
        position: absolute;
        top: 30px;
        left: 100px;
      }

      /* .box1 .inner1 {
        position: relative;
        top: 30px;
        left: 100px;
      }

      .box2 .inner1 {
        position: absolute;
        top: 30px;
        left: 100px;
      } */
    </style>
  </head>
  <body>
    <div class="outer">
      <div class="box box1">
        <div class="inner inner1">inner1</div>
        <div class="inner inner2">inner2</div>
      </div>
    </div>
    <div class="outer">
      <div class="box box2">
        <div class="inner inner1">inner1</div>
        <div class="inner inner2">inner2</div>
      </div>
    </div>
  </body>
</html>
```

## position Sticky vs Fixed

[https://developer.mozilla.org/en-US/docs/Web/CSS/Containing\_Block\#identifying\_the\_containing\_block](https://developer.mozilla.org/en-US/docs/Web/CSS/Containing_Block#identifying_the_containing_block)

#### sticky

* Sticky 를 사용하기 위해서는 top, left 등을 지정해줘야한다.
* 해당 요소가 들어있는 박스를 기준으로 위치를 잡는다.
* 박스 안의 다른 요소와 겹쳐질 수 있다.

#### fixed

* 부모와는 상관없이 viewport 에서 바로 위치를 잡는다.

```markup
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <!-- https://developer.mozilla.org/en-US/docs/Web/CSS/position-->
    <!-- https://developer.mozilla.org/en-US/docs/Web/CSS/Containing_Block -->
    <style>
      .container {
        position: relative;
        top: 100px;
        left: 100px;
        background-color: beige;
      }

      .box {
        width: 80px;
        height: 80px;
        margin-bottom: 20px;
        background-color: chocolate;
      }

      .box2 {
        background-color: hotpink;
        position: sticky;
        top: 20px;
        left: 20px;
      }

      .box3 {
        background-color: blue;
        position: fixed;
        top: 20px;
        left: 20px;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <div class="box box1"></div>
      <div class="box box2"></div>
      <div class="box box3"></div>
      <div class="box box4"></div>
      <div class="box box5"></div>
      <div class="box box6"></div>
      <div class="box box7"></div>
      <div class="box box8"></div>
      <div class="box box9"></div>
      <div class="box box10"></div>
    </div>
  </body>
</html>
```

## 수평 중간에 정렬하는 방법

크기가 큰 box 에 아이템을 넣으면 보통 아이템은 자동으로 왼쪽에 자리잡는다. 그리고 크기 만큼의 차이가 margin-right 으로 자동으로 들어간다.

### margin auto

> 블록 레벨의 요소에 사용하자. 수평에만 사용되고 수직에는 사용할 수 없다.

* auto 옵션을 넣으면 `margin-left: 0; margin-right:100` 과 같은 상황에서 `margin-left: 50; margin-right: 50` 으로 자동으로 변경된다.
* **margin 으로는 수평적으로만 정렬이 가능하다.**

### text-align

> 블록 레벨에는 적용되지 않는다.
>
> 수평에만 사용되고 수직에는 사용할 수 없다.

* text 가 아니더라도 대부분의 요소를 정렬할 수 있다.

#### 주의할 것

`div` 요소와 `button` 요소가 있다고 해보자, `text-align:center` 을 했을 때 `div` 는 중앙정렬이 되지 않고 `button` 은 중앙정렬이 된다. 왜?

`div` 는 한줄에 하나만 올 수 있는 요소이기 때문에 자동으로 **margin-right** 가 생겨버린다. 따라서 text-align 이 먹히지 않음

결론은 block-level 요소들은 자동으로 margin 이 생기기 때문에 `margin auto` 를 사용하고 `inline-level` 요소들은 `text-align` 을 사용하면 된다.

## 수직 정렬하는 방법

### transform 을 이용하기

> Transform 을 사용하면 움직이는 거 로테이션 하는 것들이 가능하다.

```css
.inner3 {
  transform: translate(50%, 50%); 
}
```

### text 수직 정렬하기

> 이 방법은 두줄 이상의 텍스트를 입력할 때 문제가 생긴다..

`text-align` 을 이용해서 수평정렬하고

`line-height`을 부모 박스의 height 만큼 지정하면 된다. 이 원리는 text 한줄의 높이를 박스 사이즈만큼으로 지정함으로써 글자를 가운데 정렬하는 뭔가 트리키한 방법이다.

## background

[https://developer.mozilla.org/en-US/docs/Web/CSS/background](https://developer.mozilla.org/en-US/docs/Web/CSS/background)

[https://developer.mozilla.org/en-US/docs/Web/CSS/background-image](https://developer.mozilla.org/en-US/docs/Web/CSS/background-image)

## transform

[https://developer.mozilla.org/en-US/docs/Web/CSS/transform](https://developer.mozilla.org/en-US/docs/Web/CSS/transform)

트랜스폼을 이용하면 굉장히 여러가지 값을 움직일 수 있다.

* x, y, z 축
* 3d
* Scale

x, y, z 축을 움직일 때 본인의 원래의 위치에서 마음껏 움직일 수 있는듯.

### translate

* x, y, z 축을 기준으로 움직인다.

### scale

* 지정한 값만큼 사이즈를 키운다.

### rotate

* 컨테이너를 회전시킨다
* 단위는 `deg`

## transition

[CSS Transition](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)

[Animation timing function](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timing-function)

[Cubic bezier](https://cubic-bezier.com/)

cubic 사이트를 통해서 우리가 `transition-timing-function` 을 지정할 수 있다.

## CSS variable

[https://developer.mozilla.org/en-US/docs/Web/CSS/--\*](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)

CSS에서 변수를 정의하고 쓸 수 있는 곳은 아무 곳에서나 가능하다.

변수 선언은 `--` 를 이용해서 선언한다. 이후에 사용하려면 `var` 를 이용해서 변수를 사용한다. 선언된 변수는 **모든 자식 노드에서 사용이 가능하다.**

따라서 **root** 에서 변수를 선언하면 이후에는 모든 요소에서 변수를 사용할 수 있게 된다.

특히 **media-query** 를 이용해서 화면의 크기가 바뀔 때 유용하다. 왜냐하면 `root` 에 변수 선언만 가져가서 값만 변경하면 되기 때문!

## data attributes

> 이 데이터는 html 파일에 적나라하게 보이기 때문에 사용자에게 민감한 데이터는 절대로 여기에 넣으면 안된다.

우리가 원하는 데이터를 DOM 요소에 추가할 수 있게 해준다. 사용하기 위해서는 `data-` 다음에 사용하면 된다.

Data 는 key-value 값을 갖는다.

`<div data-index="1"> </div>div>` 라는 라인이 있을 때 키 값은 `index` 가 되고 `value` 값은 1이 된다.

### CSS 로 선택하기

CSS 에서 위와같은 `data` 속성을 선택하기 위해서는 `[ ]` 를 이용하면 된다.

```text
[data-index="1"] {
  이렇게 하면 된다.
}

div[data-index="1"] {
  이렇게 하면 된다.
}
```

### JS 로 선택하기

```javascript
const index = document.querySelector("div[data-index='1']");
```

