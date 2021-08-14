# 블록은 실행이 보장된다

```javascript
// 1번 코드
const button = document.querySelector('button');
button.addEventListener('click', () => {
  const element = document.createElement('h1');
  document.body.appendChild(element);
  element.style.color = 'red';
  element.style.innerText = 'hello world';
})

// 2번 코드
const button = document.querySelector('button');
button.addEventListener('click', () => {
  const element = document.createElement('h1');
  element.style.color = 'red';
  element.style.innerText = 'hello world';
  document.body.appendChild(element);
})
```

> 위에서 1번 코드와 2번코드의 차이점은, 2번에서는 DOM 요소의 모든 속성을 처리하고나서 append 을 해주고 1번은 append 을 한 다음에 속성 처리를 해준다.

## 만약 1번처럼 코딩을 하면 아래와 같은 상황이 발생할 수 있지 않을까?

1. 생성된 DOM 을 `body` 에 `append` 한다  
2. `append` 가 되는 순간 `브라우저` 에서 렌더링을 한다.
3. 즉 바로 아래줄에서 세팅해주는 `style` 들이 지정되지 않아서 원치 않는 결과나 나온다.

### 아니다. 문제는 발생하지 않는다.

> 콜스택에서는 실행되고 있는 블록이 보장된다.

그 이유는 이전에 말했듯이 **콜스택에서 현재 실행되고 있는 블록은 보장된다.** 라는 법칙 때문이다. 따라서 버튼을 누르면 콜백으로 등록한 함수가 JS의 콜백으로 들어가고 이 콜백은 한번 시작되면 반드시 보장된다.

보장이 된다는 말의 의미는 버튼을 클릭하고 Event LOOP 을 통해 JS 콜스택으로 들어온 `arrow-function` 인 아래의 코드 블록은 전체가 실행이 끝날때까지 다른 동작을 하지 않는다는것

```javascript
{
  const element = document.createElement('h1');
  document.body.appendChild(element);
  element.style.color = 'red';
  element.innerText = 'hello world';
}
```

## 다른 예시

```javascript
button.addEventListener('click', () => {
  box.style.transition = `transform 1s ease`;
  box.style.transform = `translateX(800px)`;
  box.style.transform = `translateX(500px)`;
})
```

위 코드는 아래와 같은 생각으로 작성되었다.

1. 박스의 X크기를 800px 로 늘리면서 애니메이션 효과를 준다. 
2. 크기가 커진 박스의 X 크기를 500px 로 줄이면서 애니메이션 효과를 준다.

> 결국 박스가 커졌다 작아지는 애니메이션의 효과를 준다.

### 하지만... 박스의 크기는 500px 로 작아지기만 한다.

왜? 인지는 첫번째 들었던 예시를 통해 알수있듯이 **EVENT LOOP이 해당 콜백을 실행하기 시작하면 최종적으로 평가되는** `box.style.transform = translateX(500px)` 이고 EVENT 가 한바퀴 더 돌아서 렌더링 되는 시점에서는 `box.style.transform = translateX(500px)` 만 있기 때문.

