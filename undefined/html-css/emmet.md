# emmet

## emmet 사용하기!

`!`: 기본적인 html 을 만들어준다.

`.` : class 생성자

`#` : id 생성자

### 자식과 같이 만들기

`div>ul>li` 을 사용하면 자식을 만들 수 있다.

```markup
<div>
  <ul>
    <li></li>
  </ul>
</div>
```

### 형제 만들기

`div>ul+ol`

```markup
<div>
  <ul></ul>
  <ol></ol>
</div>
```

### 반복적으로 만들기

`div>li*5`

```markup
<div>
  <ul></ul>
  <ol></ol>
</div>
```

### 그룹화하기 - 괄호 \( \) 사용하기!

div&gt;\(header&gt;ul&gt;\(li&gt;a\)\*2\)+footer&gt;p

아래와 같은 코드르 빠르게 작성하고 싶다고 하자.

```markup
<div>
  <header>
    <ul>
      <li><a href=""></a></li>
      <li><a href=""></a></li>
    </ul>
  </header>
  <footer>
    <p></p>
  </footer>
</div>
```

`div>(header>ul>li*2>a)+footer>p` 처럼 하면 된다

### 태그 안에 텍스트 넣기 -  { } 사용 하기

`p{hello world!}`

```markup
<p>
  hello world!
</p>
```

### 자동 숫자 넣기 - $ 사용

```markup
<div class="class1">item 1</div>
<div class="class2">item 2</div>
<div class="class3">item 3</div>
<div class="class4">item 4</div>
<div class="class5">item 5</div>
```

위와같이 하고 숫자를 넣어주고 싶은데 어떻게해?

`.class${item $}*5` 으로 하면 된다.

### 더미용텍스트 - lorem

`p>lorem`

`p>lorem4` 숫자를 넣으면 단어의 수를 조정할 수 있다.

