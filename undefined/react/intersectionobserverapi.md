# IntersectionObserverAPI로 무한스크롤 구현

{% hint style="info" %}
[MDN IntersectionObserver API](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)

[참고한 유튜브 영상](https://www.youtube.com/watch?v=GVDiw3lAyp0&ab_channel=LeighHalliday)

[내 코드](https://github.com/simian114/infiniteScroll)

[IntersectionObserver API v2](https://web.dev/intersectionobserver-v2/)
{% endhint %}

## Intersection Observer는 어떤 문제들을 해결하기 위해 등장했을까?

기존에는 스크롤바에 기반해서 무한스크롤을 구현했다. 그렇게 하기 위해서는 아래처럼 박스가 되는 곳에 스크롤에 대한 이벤트를 걸어야 했다.

```text
<StyledCardListContainer onScroll={handleScroll}>
  {comments.map((comment) => (
    <Card key={comment.id} comment={comment} />
  ))}
</StyledCardListContainer>
```

이렇게 하게 됨으로써 생기는 문제가 있다. 바로 **스크롤이 될 때 마다 `handleScroll` 이 동작하는 것이다.** `handleScroll` 메서드는 결국 자바스크립트의 메인엔진에서 실행이 되어야하므로 많은 부하가 걸리게 된다.

이런 문제를 해결해 줄 수 있는게 바로 `intersection-observer API` 이다.

### 그렇다면 어떻게 위의 문제를 해결해줄 수 있을까?

간단하다. `intersection observer API` 는 `Web API` 이기 때문이다!! 즉, 스크롤이 일어날 때 마다 자바스크립트의 코드를 곧잘 돌려야하는 이전과는 달리 해당 API 를 사용하면 자바스크립트 엔진과는 상관없이 브라우저단에서만 무한스크롤 로직을 검사하고, 조건을 통과하는 경우에는 자바스크립트의 코드로 데이터를 불러오면 되는것!

## Intersection Observer에 대해 알아보자.

### intersection observer의 흐름을 알아보자

> `intersection observer` 는 무한스크롤에만 사용되는건 아니다...

이 `API`는 크게 **관찰자\(observer\)** 와 **관찰 대상\(entry\)**, **옵션\(조건\)** 그리고 **콜백함수\(로직\)** 이 존재한다.

1. `관찰자`를 생성한다.
2. `관찰 대상`을 생성한다.
3. `관찰자`는 `관찰 대상`을 관찰한다
4. `관찰 대상`이 `조건`을 만족하는 상태에 놓이게 된다면 `콜백 함수`를 실행한다.

### 코드로 알아보자

#### 관찰자의 생성\(관찰자, 옵션, 콜백함수...\)

> [https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver/IntersectionObserver](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver/IntersectionObserver)
>
> [https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver)

```javascript
const observer = new IntersectionObserver(callback[, options]);
```

1. `callback`은 콜백함수다 즉 조건이 만족하면 실행할 함수
2. `options` 는 각종 조건들을 넣을 수 있다. 자세한건 [MDN 사이트](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver/IntersectionObserver#%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98)에서 보자. 
   * 가장 중요한점은 `threshold` 다. `관찰 대상`이 화면에 `어느정도` 보이면 조건이 참인지를 결정하는 요소다. 매우 중요하다.
3. [리턴값](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver)\(**observer**\) 은 `관찰자` 가 된다. 자세한 사용법은 [링크](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver)를 참고하자.
   1. [observe](https://developer.mozilla.org/ko/docs/Web/API/IntersectionObserver/observe)
      * `관찰 대상`을 지정할 수 있다. **`관찰 대상`은 하나 이상일 수 있다!!, 다만 그 조건은 변하지 않는다.**
   2. [unobserve](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserver/unobserve)
      * `관찰 대상`에 대한 관찰을 중지한다.

#### 관측 대상의 생성

```javascript
// 1. 관찰자를 생성합니다.
const observer = new IntersectionObserver(callback[, options]);

// 2. 관측 대상을 생성합니다.
const target = document.querySelector('#target');
observer.observe(target);
```

이제 `target` 이 특정 조건을 만족하게 되는 경우 `callback` 메서드를 호출한다.!

#### 조건이 만족하게 되면 어떤 모습으로 `callback`이 실행될까?

> callback을 제대로 사용하기 위해서는 intersection-observer API 제시하는 방향으로 만들어야한다.
>
> [IntersectionObserver 생성하기](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API#intersection_observer_%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)
>
> [IntersectionObserverEntry](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry)

[해당 링크](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API#intersection_observer_%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0) 에서 스크롤을 내려 `Targeting an element to be observed` 를 보자. 여기를 보면 `callback` 을 어떻게 정의해야하는지를 알려준다.

```javascript
let callback = (entries, observer) => {
  entries.forEach(entry => {
    // Each entry describes an intersection change for one observed
    // target element:
    //   entry.boundingClientRect
    //   entry.intersectionRatio
    //   entry.intersectionRect
    //   entry.isIntersecting
    //   entry.rootBounds
    //   entry.target
    //   entry.time
  });
};
```

즉 조건을 만족하게 될 경우 `Intersection-observer API` 는 `콜백함수` 에게 [entries](https://developer.mozilla.org/en-US/docs/Web/API/IntersectionObserverEntry) 를 주게 된다. 그리고 이 `entries` 는 **`관찰 대상`의 리스트다.** 따라서 사용자는 이 `관찰 대상의 리스트` 에서 **지금 조건을 만족한 `관찰 대상` 을 찾고 우리가 원하는 로직을 실행해주면 되늑 것이다!!!!**

## 준비는 끝났다. 리액트에서 실제 코딩을 해보자

코딩을 하기 전에 생각할것

1. `Intersection Observer` 의 `조건`으로 무엇을 넣어줄 것인가.
   * 스크롤바가 바닥에 닿으면 새로운 데이터를 불러오게 만들자.
2. `관찰 대상` 은 무엇인가?
   * `관찰 대상`은 리스트의 맨 아래에 비어있는 html 태그로 선언한다.
3. 어떤 로직\(`콜백함수`\) 를 넣어줄 것인가?
   * 새로운 데이터를 불러오는 로직을 넣는다.

### 만드는 순서

#### `관찰 대상`을 만들자

```text
function CardList() {
    ...
  return (
    <StyledCardListContainer>
      {comments.map((comment) => (
        <Card key={comment.id} comment={comment} />
      ))}
      {isLoading && <div>Loading...</div>}
      {/* 아래의 div가 관찰대상!!! */}
      {!isLoading && <div ref={setObservationTarget}></div>}
    </StyledCardListContainer>
  );
}
```

리스트의 맨 아래에 `관찰 대상` 을 만든다. 여기서 중요한건 `ref={setObservationTarget}` 다.

#### `관찰자` 를 만들자

```text
export default function CardList() {
    ...
  const observer = useRef(
    new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          callback();
        }
      },
      { threshold: 1 }
    )
  );
    ...
}
```

`관찰 대상`이 하나이므로 `콜백함수` 의 인자로 들어오는 `entries` 에서 구조화할당? 을 이용해 `entry` 하나만을 가져온다. 그리고 이 `entry` 의 속성인 `isIntersecting` 를 이용해 조건을 검사하고, `콜백함수` 를 실행한다.

자, 이렇게 `관찰자`, `관찰 대상`, `조건`, `콜백함수` 를 다 만들었으니 끝난걸까? 아니다. 지금 이대로 실행하면 절대 원하는 결과를 얻지 못한다. **왜? `관찰 대상` 은 새로운 데이터를 가져올 때 마다 변해야하기 때문!!!**

#### `관찰 대상`을 수시로 변경해주자

> 리액트에서 무언가 변하고 이에대한 처리를 해줄 때 사용하는 훅이 있다. 바로 `useEffect`

1. 스크롤을 내린다.
2. `관찰 대상`을 만나고, 조건을 만족시킨다.
3. 새로운 데이터를 가져온다.
   * 이 때 `state` 의 `loading` 이 `true` 이므로 `관찰 대상` 이 사라진다.
4. 새로운 데이터를 리스트에 추가한다.
   * `loading` 이 다시 `false` 가 되므로 `관찰 대상` 이 다시 렌더링된다.
5. `관찰 대상` 이 다시 렌더링 됐지만, **3번과 4번의 과정에서 지워지고 새롭게 만들어졌다.**
   1. 따라서 지워진 `관찰 대상` 은 `관찰 대상 리스트` 에서 제거해줘야하고
   2. 새롭게 `관찰 대상`을 지정해줘야한다.

이런 로직을 담당할 수 있게 해주는 코드는 아래와 같다.

```text
...
    useEffect(() => {
    const currentTarget = observationTarget;
    const currentObserver = observer.current;
    if (currentTarget) {
      // 5-2
      currentObserver.observe(currentTarget);
    }
    // 5-1
    return () => {
      if (currentTarget) {
        currentObserver.unobserve(currentTarget);
      }
    };
  }, [observationTarget]);
...
```

### 관련 로직을 커스텀훅으로 만들자

> 왜 커스텀 훅으로 만들어야하는가? 해당 로직을 따로 분리하면 분명 다른 프로젝트에서 사용할 수 있을거로 보이기 때문!

#### 코드

```text
import { useEffect, useRef, useState } from 'react';
import 'intersection-observer';

export const useIntersectionObserver = (callback) => {
  const [observationTarget, setObservationTarget] = useState(null);
  const observer = useRef(
    new IntersectionObserver(
      ([entry]) => {
        if (!entry.isIntersecting) return;
        callback();
      },
      { threshold: 1 }
    )
  );

  useEffect(() => {
    const currentTarget = observationTarget;
    const currentObserver = observer.current;
    if (currentTarget) {
      currentObserver.observe(currentTarget);
    }
    return () => {
      if (currentTarget) {
        currentObserver.unobserve(currentTarget);
      }
    };
  }, [observationTarget]);

  return setObservationTarget;
};
```

#### 의문점

1. `useEffect` 에서 `clean-up` 을 꼭 해줘야하는가?
   * 해줘야한다!! 실행상에 문제는 없다. 다만 이전의 타겟이 남아있기 때문에 `callback` 이 쓸데없이 더 불러와지는 문제가 있다\(물론 `callback`이 실행되도 `isIntersecting`에서 걸리기 때문에 대부분의 경우 걱정할 필요가 없지만...\)
   * `clean-up` 은 `useEffect` 에서 **가장 먼저 실행된다.** 따라서 현재의 로직을 실행하기 전에, `closure`로 이전의 데이터가 존재하면 `clean-up` 해주는 것.

#### 사용법

1. `무한스크롤` 이 필요한 컴포넌트에서 해당 훅으로 `setObservationTarget` 을 선언한다. **실행할 로직을 인자로 넘겨준다!!**

   ```text
   function CardList() {
     const [isLoading, setIsLoading] = useState(false);
     const [comments, setComments] = useState([]);
     const [commentWorker] = useState(new CommentWorker());
     const fetchMoreComments = useCallback(async () => {
       setIsLoading(true);
       const newComments = await commentWorker.getMoreComments();
       setComments((comments) => [...comments, ...newComments]);
       setIsLoading(false);
     }, [commentWorker]);
     const setObservationTarget = useIntersectionObserver(fetchMoreComments);
   ...
   ```

2. 리스트의 맨 아래에 빈 태그를 만들고 `ref` 를 연결해주자.

   ```text
   function CardList() {
     ...
       return (
       <StyledCardListContainer>
         {comments.map((comment) => (
           <Card key={comment.id} comment={comment} />
         ))}
         {isLoading && <div>Loading...</div>}
         {/* 아래 코드 */}
         {!isLoading && <div ref={setObservationTarget}></div>}
       </StyledCardListContainer>
     );
   ```

