# setInterval에 클로져 개념 사용하기

{% embed url="https://kyounghwan01.github.io/blog/JS/JSbasic/intervalFunction/" caption="너무 깔끔한 정리" %}

{% embed url="https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval" caption="MDN 공식 문서" %}

기본적인 사용법은 위 사이트에서 확인하자.

## 상황

`setInterval` 이 특정 값의 조건에 맞으면 멈추게 하고 싶다. 위의 첫번째 링크를 확인해보면 아래와 같은 코드가 있다.

```javascript
var interval = setInterval(function() {
  console.log("Interval");
}, 1000);

clearInterval(interval);
```

위의 코드와 클로저의 개념을 사용해서 특정 조건을 추가해서 문제를 해결할 수 있을거 같다.

## 해결

```javascript
function setCountDownAsInterval() {
  let remain_time = 9;
  let myInterval = 0;

  return myInterval = setInterval(function () {
    if (remain_time === 0) {
      clearInterval(myInterval);
      myInterval = 0
      gameStop('time-out');
    }
    countDown(remain_time--)
  }, 1000);
}
```

여기서 나의 조건은 변수 `remain_time` 이 0 이 되면 `clearInterval` 을 해야하는 것. 위에 있던 **기존의 코드 한번 더 함수로 감싸서 해결했다.**

