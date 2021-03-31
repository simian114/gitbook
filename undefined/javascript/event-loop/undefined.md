---
description: 브라우저에게 맡긴다.
---

# 자바스크립트에서 어떻게 비동기적인 실행이 가능한걸까?

{% embed url="https://olinations.medium.com/the-javascript-runtime-environment-d58fa2e60dd0" %}

## 자바스크립트는 싱글스레드 기반의 언어  

하나의 프로세스는 아래 4가지로 이루어진다.

1. code
2. stack
3. heap
4. data

프로세스안에는 쓰레드가 여러개가 존재할 수 있다. 만약 멀티쓰레딩의 프로그램이 있다면 각각의 쓰레드들은 **code, heap, data를 공유하고 각 쓰레드 자신만의 stack을 갖는다.**

이전에 학습했었던 C언어에서 쓰레드가 자원을 공유하는 특성을 이용해서 **철학자문제** 를 해결했던 경험이 있다.

### 자바스크립트는 싱글스레드 언어인데..?

그런데 **자바스크립트는 싱글 스레드 언어다.** 싱글 스레드라는건 **함수가 호출되어서 쌓이는 스택이 하나뿐이라는 것.** **이 말은 즉 한번에 하나의 일밖에는 수행하지 못한다는 것이다.**

흠... 그러면 지금까지 비동기적으로 실행되는 코드들은 도대체 어떻게 수행되던걸까? 분명 **setInterval, promise, setTimeoue**  등 멀티쓰레딩 처럼 동작하는 것처럼 보일때가 많았는데 말이지.

비밀은 **자바스크립트의 런타임 환경**에 있다.



## 자바스크립트 런타임환경

![&#xC790;&#xBC14;&#xC2A4;&#xD06C;&#xB9BD;&#xD2B8; &#xB7F0;&#xD0C0;&#xC784; &#xD658;&#xACBD;](https://miro.medium.com/max/700/1*zeKjWCjyAGZ9JN4fvnWsiA.png)

위의 사진이 바로 JS의 런타임 환경이다. 구성원들이 보이는가?

1. HEAP\(JS ENGINE\)
   * 메모리가 할당되어 있는곳. 여기에 있는 메모리는 파편화되어 저장되어 있다.
2. CALL Stack\(JS ENGINE\)
   * 코드가 쌓여서 실제로 수행되는 곳
3. Web APIs
   * setTimeout 등이 **call stack** 에서 실행되면 스택에서 사라짐과 동시에 **Web APIs** 에서 동작한다. 특징은 해당 api의 실행이 끝날 때 callback 을 task queue 로 넘긴다는 것
4. CallBack queue
   * web api의 실행이 끝나고 등록되어있던 call back 이 쌓이는 곳
   * micro queue 와 task queue
5. **Event Loop**
   * CallBack queue 에 쌓여있는 JS의 callback 메서드들을 **JS의 콜스택으로 넘겨준다.**

**JS가 멀티쓰레딩처럼 동작하는 이유가 여기있다. 그렇다. Web API를 호출하면 JS는 콜스택에서 실행함과 동시에 해당 일을 WEB 브라우저에게 맡긴다. 그리고 JS는 자기가 할 일을 묵묵히 수행한다.**

