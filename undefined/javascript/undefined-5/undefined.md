# 이벤트 임시 정리

### 프로세스랑 쓰레드의 차이는 무엇인지

프로세스는 운영체제 위에서 연속적으로 실행되고 있는 프로그램들이다. 각각의 프로그램들은 프로세스를 갖고 있다.

프로세스는 메모리 위에서 실행되고 있다. 프로세스는 독립적으로 실행된다. 각 프로세스는 저마다 할당된 자원이 있다.

프로세스가 사용하는 자원들은 아래와 같다.

1. code
   * 정말 그 코드
2. stack
   * 콜스택. 현재 실행되는 함수가 종료되면 다음에 실행해야 할 함수가 쌓여있음
3. heap
   * 동적
4. data
   * 전역변수

쓰레드는 한 프로세스 안에 존재함. 하나의 프로세스에 여러개의 쓰레드가 존재할 수 있다. 쓰레드는 프로세스의 **Code, Heap, Data 자원을 공유한다. 그리고 쓰레드 마다으 Stack 이 있다.**

쓰레드는 동시 다발적으로 일을 할 수 있기 때문에 프로세스의 동작을 도와준다.

여기서 포인트는 쓰레드는 자기가 일을 할 때 어디에서브투 어디까지 일을 했고 그 다음에는 어디로 가야하는지를 기억하고 있는 고유의 스택이 지정되어져 있지만 데이터나 코드나 힙 같은 공통적인 데이터의 리소스는 프로세스에 있기 때문에 쓰레드들은ㅇ 이 프로세스에 공통적으로 할당된 리소스에 동시 다발적으로 접속해서 동시다발적으로 변경하는 공유의 형식으로 이루어진다.

### 자바스크립트 런타임 환경

자바와 같은 언어는 언어 자체에서 멀티쓰레딩이 지원이 된다. 이 말은 사용자가 직접 쓰레드를 지정해서 코딩을 할 수있다는 것.

**하지만 자바스크립트는 싱글쓰레드 언어!!** 즉 언어 자체에는 멀티쓰레딩이 없기 때문에 사용자가 쓰레드를 지정해서 프로그래밍을 할 수 없다.

자바스크립트 언어 자체에서는 멀티쓰레딩이 불가능 하지만, **JS 가 동작하고 있는 브라우저라는 프로그램에서는 여러 개의 쓰레드를 돌릴 수 있다!!! 즉 웹 APIs 들을 이용하면 멀티쓰레딩이 가능해진다는 것이다.**

JS 의 런타임환경에서는 다양한 방법을 통해서 멀티쓰레딩 같은 효과를 얻을 수 있다.이런 멀티 쓰레딩 뿐만 아니라 **event-loop** 를 통해서 다양한 동작을 실행할 수가 있다.

### Web APIs와 TaskQeue 그리고 Event Loop

브라우저에서 제공하는 메서드들. 자바스크립트는 호스트 환경에 많이 기댄다. 호스트환경이란 보통 브라우저를 의미함.

JS는 앞서 싱글스레드 언어라고 이야기를 했는데 기존에 우리의 코드를 살펴보면 프라미스도 실행되고 .... setTimeout 과 같은 메서드도 실행하고... 하면서 비동기적인 프로그래밍을 했었다. 이런 비동기를 하려면 분명 멀티스레드가 필요할텐데 우리의 js 는 싱글스레드언어...

이런걸 가능하게 해주었던건 바로 **브라우저의 존재때문.** 브라우저도 하나의 프로그램이다. 그리고 브라우저는 **멀티스레딩이 가능한 프로그램이다.** 즉, Web APIs 는 브라우저에서 실행되고, 브라우저에서 멀티쓰레딩되는것이다.

자바스크립트 엔진에 있는 **call stack** 에서 **setTimeout** 을 실행했다고 하자.

1. JS의 콜스택에서 setTimeout을 브라우저로 넘긴다.
2. 브라우저에서 **setTimeout** 을 실행한다.
3. 그동안 JS는 다른일을 계속 수행한다. 즉 이때는 **병렬적으로 일이 진행되고 있는것**
4. 이제 **setTimeout** 의 시간이 다 됐다. 그러면 **callBack은 TaskQueue로 들어간다.**
5. **TaskQueue** 에 들어간 콜백은 JS의 콜백을 보고 일이 없다면 큐에서 빠져나와서 JS의 콜백으로 들어간다.
   * **Event Loop** 이 **TaskQueue** 와 **CallStack** 을 감시하면서 태스크큐에서 콜백을 빼고 콜스택으로 넣는다.
   * 콜스택이 텅텅 비었을 때 넣는다.
6. 콜스택에 들어간 콜백이 자바스크립트 엔진에서 실행된다.  

**중요한 건, 콜스택에서 지금 실행중인 블럭은 끝날 때까지 보장이된다. 즉 중간에 다른 일들을 할 수 없고 현재의 코드 블럭이 끝날때까지 event Loop가 기다렸다가 다른 밑에 있는 콜스택이 수행되거나 Task Queue에 있는 코드가 실행이된다.**

### Micro Task Queue 와 Render Sequence

Task queue 는 Web APIs 에서 우리가 등록한 콜백 함수를 특정한 이벤트가 발생했을 때 즉 지정된 이벤트가 발생했을 때 태스쿠 큐에 넣는다.

**마이크로 태스트 큐에는 우리가 흔히 사용하는 프로미스에 등록된 콜백과 Mutation observer 라는 Web API에 등록된 Call Back 이 들어간다.**

우리가 만약 fetch 를 이용해서 프로미스를 만들었다면 then 이후에 실행이 되는 콜백은 마이크로 태스크 큐에 들어온다.

#### 렌더

주기적으로 화면을 업데이트 하는것.

Request animation frame 라는 콜백을 등록해놓으면 다음에 브라우저가 업데이트 되기전에 내 콜백을 실행해줘 라는 API 가 있다. 그떄 우리가 호출하는 콜백은 **request animation frame Queue에 차곡차곡 쌓인다.** 이제 **렌더링 트리가 만들어지기 전에 request animation frame queue 에 있는 콜백들을 먼저 실행하고 렌더 작업을 한다.**

### 정리

#### Task Queue

흔하게 사용한 콜백들이 들어오는 큐

#### Micro Task Queue

프로미스에 등록된 콜백 또는 mutation observer 에 사용된 콜백

#### Request animation frame QUEUE

request animation frame 에 등록해놓은 콜백. 렌더가 실행되기 전에 콜백들이 실행된다.

> 어떻게 브라우저는 이 많은 것들을 동시에 처리할 수 있는거지?

그러면 어떻게 위의 것들을 순서대로 잘 처리할까?

> 이벤트루프가 call stack, Render, MicroTask Queue, TaskQueue 를 빙글빙글 돌면서 감시한다! 이것들을 감시영역이라고 일단 부르자?

1. 이벤트 루프는 while\(1\) 처럼 죽지 않고 계속 살아서 감시하고 있는다.
2. call stack 에 함수가 들어가 있다면 이벤트루프는 무작정 도는걸 그만하고 이 콜스택위에 있는 함수를 감시한다. 만약 이 call stack에서 실행되고 있는 함수가 시간을 오래잡아먹는다면 이벤트루프는 계속 거기에만 머물고 있어서 브라우저의 화면이 업데이트 되지 않는다.
3. 이렇게 이벤트루프가 머물고 있다가 함수가 종료되면 다시 빙글빙들 돌게된다.
4. **이벤트루프는 RENDER 에 갈수도 안갈수도 있다.**
   * 브라우저에서는 우리가 업데이트 하는 내용들을 사용자에게 60FPS 로 보여주려고 노력한다.
     * 16.7ms 당 업데이트가 일어나야한다.
   * 그런데 **event Loop** 는 감시영역을 한바퀴 도는데 보통 1ms도 걸리지 않는다.
   * 따라서 감시영역을 돌 때 마다 RENDER 을 업데이트 할 필요가 없는것.
5. 이벤트루프가 Render 을 생략하고 **mirco task queue** 로 왔다
   * 이벤트루프는 콜스택으 상황을 보고 콜스택에 쌓여있는게 없다면 **마이크로 태스트 큐에 있는 아이템들을 하나하나 콜 스택에 순서대로 넣는다.**
   * 여기서 주의할 건. 콜스택에 콜백함수를 하나 넘기고,  마이크로 태스크 큐에 들어있는 나머지들은 마이큐로 태스크 큐에서 대기하고 있는다.
   * 그러는 동안 JS 콜백에서 콜백이 다 실행이 되면 이벤트 루프는 다시 마이크로 태스크 큐에 있는 아이템을 JS 콜백으로 넘긴다...
   * 이런 작업을 **Micro Task Queue가 빌 때까지 반복한다. 만약 이런 와중에 Micro Task Queue 에 콜백이 더 쌓이면 이것들도 다 처리한다.**
   * 즉 **마이크로 태스크 큐가 텅텅 빌 때 까지 EVENT LOOP 는 여기에 머물면서 콜백을 실행한다.**
6. 이제 **Task Queue로 온다**
   * **Task Queue 에서는 한번에 딱 하나의 아이템만 콜스택으로 옮긴다!!!**
   * 마이크로 태스크 큐에서는 전부 빌떄까지 계속 이벤트루프가 머물렀지만 여기서는 딱 한번!!
7. 어 이제 브라우저의 화면을 업데이트할 때가 됐는데?? -- RENDER로 온다
   * 그러면 **Request Animation Frame 을 통해서 등록된 CallBack들을 하나하나씩 실행한다.**
   * 그리고 Render Tree, Layout, paint 등의 작업을 한다.
   * 이 과정이 끝나면 화면이 업데이트 된다.
8. 이렇게 계속 이벤트 루프는 돈다...

### 간단한 코드와 설명

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

위에서 1번 코드와 2번코드의 차이점은, 2번에서는 DOM 요소의 모든 속성을 처리하고나서 append 을 해주고 1번은 append 을 한 다음에 속성 처리를 해준다.

위 코드에서 1번과 2번은 각각 어떻게 동작할까? 1번이 append 를 한 다음에 속성을 세팅해주는데 여기에서 무언가 문제가 발생하지는 않을까?

1번이 안될거 같은덴 그 이유는 **append 를 한 시점에 브라우저가 렌더링되서 화면에 스타일이 적용되지 않은 값이 나오고 그 다음에 스타일이 적용될 수 있다고 생각할 수 있기 때문**

#### 정답은 위 두 코드는 모두 잘 동작한다.

그 이유는 이전에 말했듯이 **콜스택에서 현재 실행되고 있는 블록은 보장된다.** 라는 법칙 때문이다. 따라서 버튼을 누르면 콜백으로 등록한 함수가 JS의 콜백으로 들어가고 이 콜백은 한번 시작되면 반드시 보장된다. 그리고 그 이후에서야 다른 렌더링이든 뭐든지간에가 실행이된다.

### 뭔가 다른 예시

> 10-7강의

```javascript
button.addEventListener('click', () => {
  box.style.transition = `transform 1s ease`;
  box.style.transform = `translateX(800px)`;
  box.style.transform = `translateX(500px)`;
})
```

버튼을 클릭하면 오른쪽으로 800 픽셀로 늘어났다고 500픽셀로 줄어드는걸 원해서 위와 같은 코드를 구현했다. 이게 생각처럼 동작할까?

#### 아니다.

그렇다면 왜? 동작을 하지 않을까? 왜냐면, 결국에는 최종적으로 평가된 값이 딱 한번만 렌더링 되기 때문이다. 따라서 **translateX\(500px\)** 만 동작한다. 이 콜백을 다 수행할 때 까지 이벤트 루프는 렌더링으로 넘어가지 않는데, 이 코드 블록이 다 수행되었을 때는 **translateX\(800px\) 는 아래의 코드로 인해 구체화되기 때문..**

### JS콜백에서 실행되는 함수는 최대한 가볍게 만드는게 좋다.

## 브라우저 죽이기

```markup
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    button:hover {
      background-color: pink;
      cursor: pointer;
    }
  </style>
</head>

<body>
  <button>while(true</button>
  <script>
    const button = document.querySelector('button');
    button.addEventListener('click', (event) => {
      while (true);
    });
  </script>
</body>

</html>
```

위처럼 코드를 만들고 버튼을 누르면 어떻게 될까? 브라우저가 죽는다... 왜냐하면 JS는 콜스택에 있는 블럭을 끝낼때까지 **이벤트루프가 움직이지 않는데, 위처럼 만들면 버튼을 누르는 순간 콜백 메서드를 실행하게 되고 해당 메서드는 절대로 끝나지 않기 때문에 이벤트 루프가 움직이지 못하고, 그 덕분에 화면도 다시는 렌더링 되지 못하게 된다.**

위의 예시로 콜백에서 무언가가 실행이 되고 있을 때 이벤트 루프가 움직이지 않는다는걸 증명했다!!

교훈은... 콜백 메서드로 실행되는 녀석들을 가볍게 만들자..

## setTimeout 의 비밀

```javascript
<body>
  <button>continue with setTimeout</button>
  <script>
    function handleClick() {
      console.log('handleClick');
      setTimeout(() => {
        console.log('setTimeout');;
        handleClick();
      }, 0);
    }
    const button = document.querySelector('button');
    button.addEventListener('click', (event) => {
      handleClick();
    });
  </script>
</body>
```

위와 같이 코드를 만들고 버튼을 누르면 **무한정으로 handleClick 함수가 Task Queue로 들어오게 된다.**

어떤일이 발생할까? **아무일도 발생하지 않는다!!** 문제가 발생하지 않는다!!!

1. 버튼을 누른다
2. 0ms 뒤에 콜백을 task queue 로 넘긴다
3. Task queue 의 콜백 메서드를 JS 스택으로 넘긴다
4. JS 스택에서 콜백 메서드가 실행되는데 이 때 setTimeout을 실행
5. setTimeout으 ㅣ콜백이 다시 Task Queue로 들어간다
6. 그럼 이게 또 JS 의 콜백으로 넘어가고
7. ... 반복...
8. **참고로 태스크 큐에서는 하나씩만 JS 콜스택으로 넘어간다.**
9. **이렇게 처리하는 와중에 가끔씩 render에 가서 렌더링도 하고 그런다!!!**

## 프라미스의 비밀!

> 프라미스에 넣어둔 콜백은 Micro Task Queue로 들어간다.

Micro task queue 는 일반 task queue 와는 다르게 한번에 하나가 아닌 큐 자체가 비어질 때 까지 JS 콜스택에 넣는 행동을 반복한다.\(한번에 하나씩.\)

그리고 **event loop가 여기에 머무르는 한 새롭게 들어오는 모든 콜백을 다 처리한다.** \(그러면 콜백이 늦게 실행되면 마이크로태스크 큐에 작업이 계속 쌓이게 될수도 있다는 의미겠지?\)

```markup
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    button:hover {
      background-color: pink;
      cursor: pointer;
    }
  </style>
</head>

<body>
  <button>Continue with promise</button>
  <script>
    function handleClick() {
      console.log('handleClick');
      Promise.resolve(0)
        .then(() => {
          console.log('then');
          handleClick();
        });
    }
    const button = document.querySelector('button');
    button.addEventListener('click', (event) => {
      handleClick();
    });
  </script>

</body>
```

버튼을 클릭하면 로그가 계속 출력은 되는데, **브라우저에서 렌더링이 새롭게 되지는 않는다!!!!!** **while true 를 했을 때랑 비슷한데?!**

이유는 **event loop가 micro task queue 에 머무르면서 콜백을 JS 콜스택으로 넘기면서 실행하는데**

1. JS 콜스택에서 handleClick가 실행이된다.
2. 내부에서 promise 를 실행한다.
3. Web Api 에서 이를 처리한다.
4. 1 ~ 2 번의 작업이 끝나기 전에\(event loop가 micro queue task 에서 다른곳으로 옮겨가기전에\)
5. 3번에서의 web API\(promise\)의 결과로 콜백이 micro task queue로 들어온다.
6. **즉 Event Loop 가 micro task queue 를 벗어나지를 못한다!!**

## RAF 의 비밀 \(RequestAnimationFrame\)

[https://www.youtube.com/watch?v=9XnqDSabFjM](https://www.youtube.com/watch?v=9XnqDSabFjM)

```markup
<body>
  <button>RequestAnimationFrame</button>
  <script>
    const button = document.querySelector('button');
    button.addEventListener('click', (event) => {
      requestAnimationFrame(() => {
        document.body.style.backgroundColor = 'beige';
      });
      requestAnimationFrame(() => {
        document.body.style.backgroundColor = 'orange';
      })
      requestAnimationFrame(() => {
        document.body.style.backgroundColor = 'red';
      })
    });
  </script>
</body>
```

**requestAnimationFrame 는 Web API 다!!** 이 메서드에 콜백으로 들어가는 메서드는 브라우저가 다음에 렌더링을 하기 전에 실행이되는걸 보장된다.

그 말은 즉슨, **브라우저야 다음에 너의 화면이 업데이트하기전에 내가 등록한 이 콜백 함수들을 수행해줘!**

자ㅑ 그러면 위의 코드에서 버튼을 클릭하면 어떤 일이 일어날까?

1. 버튼이 눌러지면 버튼의 콜백을 **task queue**에 넣는다.
2. **task queue** 에 넣어진 메서드는 이후에 이벤트 루프에 의해 JS의 콜스택으로 가게 된다.
3. 콜스택에 있는 콜백 메서드는 실행이 되는데 **requestAnimationFrame** 을 세번 실행하게 된다.
4. 이 결과로 **request Animation Frame web api 가 실행이 된다.**
5. 웹 API 의 호출 결과로 **Request Animation Frame 을 담당하는 큐에 들어가고, 이 큐에 들어간 콜백 메서드들은 다음번 렌더링이 되기저에 실행이 됨이 보장된다.**
6. 이제 **event loop가 렌더링에 오게 된다면 앞서 등록해 놓은 콜백들이 실행이된다.**
   1. 스타일을 베이지색으로
   2. 오렌지색으로
   3. 최종적으로는 빨강색이 된다.

> 여기서 궁금증. 그러면 스타일이 베이지색이 된다. 그러면 실제로 이게 페이지에는 적용이 되지 않고, style 값만 바꾸다가 마지막에 있는 red 만 실제로 적용이 된다음에 렌더링되는거겠지? 이전에 있는 값들은 실제로 렌더링은 되지 않고? 맞다!!!

이 RAF 는 실제로 굉장히 많이 수행된다!!!

