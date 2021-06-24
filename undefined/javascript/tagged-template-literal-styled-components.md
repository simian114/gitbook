# Tagged Template Literal\(styled-components\)

[MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals)

[참고링크1](https://styled-components.com/docs/advanced#tagged-template-literals)

[참고링크2](https://mxstbr.blog/2016/11/styled-components-magic-explained/)

### tagged template literal 이 대체 뭐야?

[설명은 링크로 대신한다.](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Template_literals) 일겅보면 `tagged template literal` 이 무엇인지는 조금은 이해할 수 있다. 하지만 왜 이 기능을 이용해서 `styled-components` 사용하는지는 아직 이해가 가지 않는다.

### tagged template literal 를 일반 함수을 사용하는 것처럼 만들어보자

보간을 사용하지 않은 일반 문자열만 건낸경우

```javascript
// Equivalent!!!
func`guess it!`
func(['guess it!'])
```

보간을 사용한 경우 \(한개의 인자\)

```javascript
const name = 'sanam';

func`guess it ${name}!`;
func(['guess it!', '!'], name);
```

첫 번째 예시와 뭔가 다랄진걸 눈치챌 수 있다. 보간으로 넣은 변수는 배열에 포함되지 않고 두 번째 인자로 들어가게 된다.

그리고 문자열들은 보간된 변수를 기준으로 `split up` 되서 배열로 첫 번째 인자로 들어간다.

두개의 보간을 사용한 예를 보자

```javascript
const name = 'sanam';
const age = 28;

func`I'm ${age} years old, ${name}`;

func(['I\'m ', 'years old, '. ''], age, name);
```

두 개 이상의 보간이 들어갈 경우에도 **문자열은 나눠져서 하나의 배열이 된다. 보간으로 들어간 변수들은 보간 순서대로 인자로 들어온다.**

### 그렇다면 왜 styled components는 Tagged Template Literal를  사용해?

시작하기 앞서 함수를 하나 만들자.

```javascript
const execFuncArgs = (...args) => args.forEach(arg => {
  if (typeof arg === 'function') {
    arg()
  }
})
```

해당 함수는 인자가 만약 함수타입이라면 실행하는 코드다. 이 함수를 이용해서 실험을 해보자.

```javascript
// 1번
execFuncArgs(`Test: ${() => console.log('called as normally')}`);

// 2번
execFuncArgs`Test: ${() => console.log('called As Tagged Template Literal')}`;
```

1번과 2번의 출력을 예측해보자. 1번을 보면 중간에 함수를 콜백으로 넘긴거 같지만, `backtick` 를 이용해서 문자열 자체를 넘기는 형태가 되므로 인자는 오직 문자열 하나만 넘겨진다. 즉 `execFuncArgs` 함수는 아무일도 하지 않는다.

2번을 분석해보자. 2번의 결과를 일반 함수 호출로 변환시켜 보면 아래와 같이 된다.

```javascript
execFuncArgs(['Test: ', ''], () => console.log('called As Tagged Template Literal'));
```

첫 번째 인자로는 문자열의 `split up` 결과인 배열이 들어가고 두 번째 인자로는 **콜백함수가 전달된다.** 따라서 `execFuncArgs` 는 콜백함수를 실행하고 `called As Tagged Template Literal` 이 출력된다.

일반함수로는 하지 못하는 문자열에 함수를 넣는 행위를 `Tagged Template Literal` 은 멋있게 할 수 있다. 이 특징을 잘 살린게 바로 `Styled-Components` 다.

`styled-componets` 를 하나 생각해보자.

```javascript
const Button = styled.button`
    width: ${({ fullWidth }) => fullWidth ? '100%' : '40px'};
`;
```

위에서 학습한 내용을 생각해보면 위 코드를 금방 이해할 수 있다.

1. `styled.button` 은 하나의 함수다.
2. 함수를 일반적인 함수호출로 변환하면 아래와 같다.

   ```javascript
   const Button = styled.button(['width: ', ''], {({ fullWidth => fullWidth ? '100%' : '40px'})})
   ```

`Tagged Template Literal` 문법을 사용했을 경우와 하지 않은 경우의 가독성, 퀄리티가 확연히 차이가 나는걸 볼 수 있다.

