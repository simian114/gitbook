# flex

### display

* flex: 컨테이너를 flex 박스로 만든다.

### flex-direction

* row: 중심축은 가로
* Row-reverse: 오른쪽에서부터 나옴
* column: 세로
* Column-reverse: 세로 뒤집어서

### flex-wrap

* nowrap: 기본값. 브라우저의 사이즈가 작아져도 해당 컨테이너가 작아질 뿐 분리되지 않는다.
* wrap: 브라우저의 크기에 맞게 컨테이너 내부의 item 들이 분리된다.
* warp-reverse: 거꾸로 래핑된다.

### flex-flow

* flex-direction 과 flex-wrap 을 한번에 설정할 수 있음.
* `flex-flow: column nowrap`

### Justify-content

> main-axis 를 기준으로 아이템들을 어떻게 정리할지 결정한다.

* flex-start: 중심축을 기준으로 왼쪽\(row\) 또는 위\(column\) 을 시작점으로 삼는다.
* Flex-end: 중심축을 기존으로 마지막 item 이 오른쪽 끝 또는 아래 끝 을 끝점으로 삼는다.
* center: 중간에 둔다
* Space-around: 아이템들 사이에 공백을 둔다.\(아이템 사이에는 양 아이템의 공백 하나씩 해서 두개의 공백이 들어가고 왼쪽 끝과 오른쪽 끝의 아이템은 각각 벽과 1개의 공백을 둔다.\)
* Space-evenly: 완전 똑같은 사이즈로 공백을 둔다.\(모든 아이템과 벽 사이에 공백이 1개\)
* space-between: 양쪽 끝에는 공백을 두지 않고 item 을 사이에만 공백을 둔다.

### align-items

> main-axis 의 반대의 정렬한다. 컨테이너 안엥서 어떻게 모든 요소들이 정렬하는지를 지정한다.

* baseline: 한 컨테이너의 아이템들안에 text 가 있는데 그 아이템들으 크기가 다르다. 그래서 한줄인데데도 한줄같지가 않을 때 이 옵션을 사용하면 됨. 옵션을 사용하면 내부 값들이 다 한줄로 보이게된다.

### align-content

> Main-axis 의 반대축에 아이템을 지정한다. 여러 컨테이너들을 정렬한다.

* Space-between: 메인축의 반대 방향으로 각 아이템들 사이에 공백을 둔다. 맨위와 맨 아래에는 공백을 두지 않는다.
* center: 중간으로 아이템들이 모인다. 

## Item의 속성

### order

> 컨테이너 내부 아이템들의 순서를 정의한다. 하지만 거의 사용되지 않는다.

* `order: 1` 처럼 사용됨

### Flex-grow

> 아이템들이 컨테이너의 크기에 맞춰 커진다.

```css
item1 {
  flex-grow: 1;
}

item2 {
  flex-grow: 2;
}

item3 {
  flex-grow: 3;
}
```

위처럼 **flex-grow** 를 설정하면 컨테이너의 전체 크기를 6으로 만들고 아래처럼 크기가 지정된다.

* item1: 1 / 6
* item2: 2 / 6
* item3: 3 / 6

### flex-shrink

> flex-grow 와 반대로 컨테이너의 사이즈가 작아질 때의 크기 변동을 지정한다.

```css
item1 {
  flex-shrink: 1;
}

item2 {
  flex-shrink: 2;
}

item3 {
  flex-shrink: 3;
}
```

값이 크면 줄어드는 정도가 그만큼 크다!

### Flex-basis

> 아이템들이 공간을 얼마나 차지하는지 세부적으로 명시해줌

* auto: grow나 shrink 에 지정된 만큼 크기가 와리가리함.
* percent 로 지정하면 된다.

```css
item1 {
    flex-basis: 60%;
}

item2 {
    flex-basis: 30%;
}

item3 {
    flex-basis: 10%;
}
```

#### Align-self

> 컨테이너에서 벗어나서 아이템 나 혼자만의 정렬되고 싶다!!

* center: 중간으로 정렬

