# 단위 정리

## CSS Units

> 사이즈를 결정해준다.

절대적인 값과 상대적인 값 두 종류가 존재한다.

## absolute

px 를 제외하고 대부분은 사용되지 않는다. 픽셀은 모니터 위에서 화면에 나타낼 수 있는 가장 작은 단위다.

픽셀을 사용하게 되면 문제점이 있는데, 컨테이너의 사이즈가 변경되어도 컨텐츠의 사이즈는 변하지 않음. 또한 사용자가 브라우저에서 폰트 사이즈를 변경해도 전혀 반응하지 않는다. 따라서 픽셀을 사용하는 건 좋지 않은 방법. 그러면 어떻게 해야해? 바로 상대적인 값을 사용하면 됨.

## Relative length units

[**MDN**](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units)

| Unit | Relative to |
| :--- | :--- |
| `em` | Font size of the parent, in the case of typographical properties like `font-size`, and font size of the element itself, in the case of other properties like `width`. |
| `ex` | x-height of the element's font. |
| `ch` | The advance measure \(width\) of the glyph "0" of the element's font. |
| `rem` | Font size of the root element. |
| `lh` | Line height of the element. |
| `vw` | 1% of the viewport's width. |
| `vh` | 1% of the viewport's height. |
| `vmin` | 1% of the viewport's smaller dimension. |
| `vmax` | 1% of the viewport's larger dimension. |

이 모든것을 다 사용하지는 않고, 자주 사용하는 단위에 대해 알아보자.

자주 사용하는 units 은 아래와 같다.

| Unit | Relative to |
| :---: | :--- |
| `em` | Font size of the parent, in the case of typographical properties like `font-size`, and font size of the element itself, in the case of other properties like `width`. 기본적으로 1em 은 브라우저에서 16px이 된다 |
| `rem` | Font size of the root element. |
| `vw` | 1% of the viewport's width. |
| `vh` | 1% of the viewport's height. |
| % | Relative to the parent element |

### em

```markup
<style>
  .parent {
    font-size: 8em;
  }
  .child {
    font-size: 0.5em;
  }
</style>

<div class="parent">parent
    <div class="child">child</div>
  </div>
```

em 은 브라우져에서 기본적으로 16px 이다. 위의 코드를 보면 `parent` 의 폰트 사이즈는 16 \* 8 이 된다.

자식은 부모에게 상대적인 크기로 결정되므로 16  _8_  0.5 의 폰트 사이즈를 갖게 된다.

em 은 % 와 동일하다. 8em 은 800%로 사용하면 된다.

### rem

em 은 부모의 크기에 상대적인 크기였다.

**rem 은 root 의 크기에 상대적이다!!** 따라서 브라우저의 기본 크기인 16px 에 상대적으로 크기가 결정된다.

### vw

> viewport 란 브라우저의 크기 자체를 말하는 것.

\(number\)vw: viewport width 의 number% 를 사용하겠다!!

* 100vw: 브라우저의 폭 전체를 사용하겠다
* 50vw: 브라우저의 폭에서 50%를 사용하겠다.

### vh

* Width 와 반대로 높이

### vmin

**브라우저의 너비와 높이 중 작은 값**에 xx를 사용하겠다.

### vmax

**브라우저의 너비와 높이 중 큰 값**에 xx를 사용하겠다.

​

