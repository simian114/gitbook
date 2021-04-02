# IntersectionObserver 를  사용해서 스크롤 이벤트의 부하 줄여주기

{% embed url="https://heropy.blog/2019/10/27/intersection-observer/" %}

{% embed url="https://developer.mozilla.org/ko/docs/Web/API/Intersection\_Observer\_API" %}

## 상황

스크롤 이벤트가 발생할 때 마다 **특정 요소들을 순회** 하면서 조건을 확인하고 특정 행동을 하는 코드가 있다고 하자.

여기서 **특정 요소들을 순회** 한다는 단어가 매우 중요하다. 왜냐하면 그 특정요소가 **한개, 두개가 아니라 수십 수백 이라면 브라우저의 성능에 분명 영향을 미칠것이기 때문이다.** 따라서 우리는 스크롤 이벤트 처럼 매우 빈번하게 일어나는 이벤트에 대해서는 특히 주의하고 코드를 짤 필요가 있다. 아래 코드를 보자.

```javascript
  window.addEventListener('scroll', () => {
    let stop;
    let boundingRect;
    let temp = 0;

    for (let i = 0; i < stepElems.length; i++) {
      step = stepElems[i];
      boundingRect = step.getBoundingClientRect();

      if (boundingRect.top > window.innerHeight * 0.1 &&
        boundingRect.top < window.innerHeight * 0.8) {
          inactivate(currentItem.dataset.action);
          currentItem = graphicElems[step.dataset.index];
          activate(currentItem.dataset.action);
        }
    }
  });
```

이 코드의 문제는 위에서 스크롤이 한번 일어날 때 마다 모든 요소의 개수 만큼 반복을 돈다는 것이다. 어떻게 하면 위의 코드를 조금 더 깔끔하게 작성할 수 있을까?

## 해결

> Intersection Observer를 사용하면 된다.

[https://heropy.blog/2019/10/27/intersection-observer/](https://heropy.blog/2019/10/27/intersection-observer/)

[https://developer.mozilla.org/ko/docs/Web/API/Intersection\_Observer\_API](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)

위의 두 링크를 보고 기본적인 이해를 하고 오자.

### 1. Intersection Observer 생성하기

```javascript
const io = new Intersectionobserver((entries, observer) => {
    // 아래의 코드는 Intersection 이 될 때 마다 갱신된다.
    ioIndex = +entries[0].target.dataset.index;
})
```

entries 에는 스크롤의 결과로 조건을 만족한 요소들의 배열이다.

### 2. 요소를 Observer에 등록하기

```javascript
for (let i = 0; i <stepElems.length; i++) {
  io.observe(stepElems[i]);
}
```

위의 코드로 감시할 요소들을 등록하자.

> 2번에서 특정 요소들을 등록했다면 이제 그 요소들이 화면 안으로 들어오거나 또는 밖으로 나갈 때 1번에서 등록한 콜백함수가 호출될 것임.

#### 3. 우리의 상황에 맞게 코드를 수정하자

여기서 말하는 우리의 상황을 정리해보자.

1. 스크롤이 올라가면 **메시지를 담은 컨테이너가 올라온다.**
2. 이 메시지 컨테이너는 화면에 최대 2개까지 보인다.
   * 두 개까지만 보이는 이유는 `margin-bottom: 60vh`가 되어있기 때문..!
3. 따라서 이전처럼 **스크롤 이벤트가 발생할 때 마다 모든 요소를 다 반복 도는게 아닌 observer 에 의해 현재 화면에 보이는 entries 의 target 을 중심으로 이전, 이후의 요소들만 반복을 돌면 되는것!!**

```javascript
 window.addEventListener('scroll', () => {
    let stop;
    let boundingRect;

    for (let i = ioIndex - 1; i < ioIndex + 2; i++) {
      step = stepElems[i];
      if (!step) continue;
      boundingRect = step.getBoundingClientRect();

      if (boundingRect.top > window.innerHeight * 0.1 &&
        boundingRect.top < window.innerHeight * 0.8) {
          inactivate(currentItem.dataset.action);
          currentItem = graphicElems[step.dataset.index];
          activate(currentItem.dataset.action);
      }
    }
  })
```

`for` 반복문을 보면 이전과 다르게 `ioIndex` 라는 변수의 앞, 뒤만을 가지고 반복을 도는걸 확인할 수 있다. 이렇게 수정함으로써 비효율을 낮출 수 있게 되었다.

