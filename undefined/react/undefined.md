# 커스텀 훅 만들기

## 커스텀 훅 만들기

먼저 함수형 컴포넌트에 대해 다시 생각해보자. 함수형 컴포넌트는 클래스 컴포넌트와 다르게 인스턴스로써 그 객체가 살아있는 개념이 아니라, **그때그때 마다 함수가 호출되는 방식이다.**

## 만들기에 앞서... 훅의 원리를 생각해본다

### 컴포넌트가 그때그때 마다 함수로써 실행된다고?

그러면 **함수형 컴포넌트는 도대체 어떻게 자신의 상태\(state\) 를 관리하는 거야?** 지금 관리되고 있는 상태도.. 어차피 다음번에 함수가 실행되면 초기화 되는건 당연한거잖아..? 이런 이슈로 그동안 함수형 컴포넌트는 상태를 관리하지 않을 때만 **성능을 위해 사용되었고, 상태가 있는 컴포넌트는 반드시 Class형으로 만들었다.**

하지만 클래스형 컴포넌트는 여간 불편한게 아니다. `this` 의 개념이 복잡하게 얽혀있고 또한 **생명주기에 관한 이슈로 많은 코드의 중복이 있다.**

이런 클래스형 컴포넌트들의 문제를 인식한 리액트 개발자들은 함수형 컴포넌트에서 상태를 관리할 수 있고 생명주기을 이용할 수 있는 기능을 추가한다. 이게 바로 **Hook 이다.**

### 그러면 어떻게 훅으로 상태를 관리할 수 있게 된거지?

> [리액트 공식문서에서 친절하게 이야기해줍니다.](https://ko.reactjs.org/docs/hooks-rules.html)
>
> [리액트는 훅이 호출되는 순서에 의존합니다.](https://ko.reactjs.org/docs/hooks-rules.html#explanation)

상태를 관리할 때는 `useState API` 를 사용합니다. 원리는 간단합니다. 함수형 컴포넌트가 실행될 때, 즉 함수가 실행될 때 `useState` 를 이용해서 우리가 관리할 값을 어딘가에 저장하고 가져오게 됩니다.

* 만약 처음 함수가 실행된다면 **어딘가에 저장을 할 것이고**
* 두 번 이상 실행된다면 **어딘가에 저장된 값을 찾아옵니다.**

복수의 `useState` 를 사용해도 각 상태는 어딘가에 저장된다. 아래와 같은 코드가 있다고 해보자.

```javascript
function Form () {
  const [name, setName] = useState('sanam');
  const [age, setAge] = useState(20);
  ...
}
```

1. `useState` 의 인자값은 **초깃값** 이다. 
2. 처음 실행될 때 `name` 은 sanam 으로 `age` 는 20 으로  세팅된다는 건 눈감고도 알 수 있다.
3. 그런데 두 번 이상 실행될 때는? 첫 번째 실행되었을 때 `name` 이 `namSSang` 으로 바뀌고 `age` 가 30이 되었다면? 초기값을 세팅해 두지 않았는데 어떻게 값이 유지가 되는거지?
4. 간단하다. 값을 `React` 의 `API` 가 저장해두기 때문에 유지해준다.
5. 두 번째 의문. 이건 매우매우 중요하다.
   * **useState의 인자값으로 들어가는건 초기값이다. 즉 현재 state 가 무엇인지는 알려주지 않는다. 그런데 어떻게, 도대체 어떻게 useState을 복수개로 사용하는데 각 값이 섞이지 않고 name은 name으로만 set되고 age는 age로서 존재할 수 있게 되는걸까?**
6. 해당 의문의 정답은 매우 심플하고도 강력한 규칙에 의해 유지된다.
   * **컴포넌트가 실행되는 순서에 의존한다.**
   * 이 말은 즉 `name` 은 처음으로 실행되는 `state` 고 `age` 는 두 번째로 실행되는 `state` 라는것이다. 리액트는 이를 기억하고 있다가 다음 실행 때 순서대로 `name` 과 `age` 를 세팅해준다.
7. 이런 규칙 때문에 **조건에 의한 state의 실행은 리액트훅에서 절대로 용납되지 않는다. 주의하자!**

## 이제 커스텀훅을 만들어봅니다.

> [리액트 공식문서를 읽어봅시다.](https://ko.reactjs.org/docs/hooks-custom.html)

커스텀 훅은 단 하나의 심플하고도 강력한 규칙인 **조건에 의한 state, effect 의 실행을 금지한다.** 만 지키면 아주 구현하기 쉽습니다. 그저 반복되는 로직만을 빼서 하나의 함수로 만들면 되는겁니다.

3가지 훅을 만들어 보겠습니다.

1. `useIntersectionObserver`
   * 무한스크롤링에 도움을 주는 `hook` 입니다. 
2. `useDebounce`
   * `mousemove`, `scroll` 등 무수히 많은 이벤트 콜백을 처리할 때 안정을 줄 수 있는 `hook` 입니다.
3. `useDetectOutsideClick`
   * `dropdown` 의 서랍이 열렸을 때, `Modal` 이 `open` 되었을 때 외부를 누르면 종료시키는 등의 기능을 부여해주는 `hook` 입니다.

### useIntersectionObserver

#### 기능

1. 감시자\(observer\) 와 감시대상\(observation target\) 을 만듭니다.
2. 감시자는 감시대상을 감시하고, 지정한 특이점\(threshold\) 을 넘어서게 되면 등록한 `callback` 을 실행시킵니다.
3. 감시대상의 변경되면, 이전 감시대상에 대한 **감시 상태를 취소하고 새로운 대상을 감지합니다.**

#### 설계

1. 인자
   * 특이점이 오면 실행할 `callback`을 받습니다. 지금은 무한스크롤을 구현하고 있으므로 데이터를 새롭게 가져오는 `API call` 을 넣어주면 됩니다.
2. 리턴값
   * **감시 대상 setter**  를 리턴합니다. 특이점이 오면 API 을 호출하고, 새로운 데이터가 세팅됩니다. 그러면 **감시대상은 바뀌게 됩니다. 따라서 훅 외부에서 감시대상을 새롭게 세팅할 수 있게 setter를 리턴합니다.**
3. 상태관리
   * 감시자
     * 감시자는 `state`로서 관리될 필요가 없습니다. 왜냐하면 감시자는 누군가의 변화를 감지하면 될 뿐, 자신의 변화는 중요하지 않기 때문입니다.
   * 감시대상
     * 감시대상은 `state`로 관리되어야 합니다. 왜냐하면 **감시대상에 변화가 생기면 감시자는 이전 감시대상에 대한 감시를 취소하고 새로운 대상을 감지하는 로직을 구현해야하기 때문입니다.**
4. effect관리
   * **감시대상** 의 변화를 감지하고 실행할 로직이 들어갑니다.

#### 구현

```javascript
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

#### 사용방법

> 무한스크롤을 예로 듭니다.

1. 새로운 데이터를 가져올 위치를 지정합니다.
   * 현재데이터의 끝으로 지정하겠습니다.
2. 현재 데이터의 끝에 보이지 않는 박스를 만들어 둡니다.

   ```javascript
   <StyledCardListContainer>
     {comments.map((comment) => (
       <Card key={comment.id} comment={comment} />
     ))}
     {lastPage && <div>더이상 불러올 데이터가 없습니다...</div>}
       // NOTE: 아래으 라인이 보이지 않는 박스가 됩니다.   
     {!lastPage && !isLoading && <div></div>}
   </StyledCardListContainer>
   ```

3. 보이지 않는 박스를 `observationTarget`으로 만듭니다.

   ```javascript
   <StyledCardListContainer>
     {comments.map((comment) => (
       <Card key={comment.id} comment={comment} />
     ))}
     {lastPage && <div>더이상 불러올 데이터가 없습니다...</div>}
       // NOTE: 아래으 라인이 보이지 않는 박스가 됩니다.   
     {!lastPage && !isLoading && <div ref={setObservationTarget}></div>}
   </StyledCardListContainer>
   ```

4. `useIntersectionObserver` 를 넣어줍니다.

   ```javascript
   function CardList() {
     const [isLoading, setIsLoading] = useState(false);
     const [comments, setComments] = useState([]);
     const [commentWorker] = useState(new CommentWorker());
     const [lastPage, setLastPage] = useState(false);

     const setObservationTarget = useIntersectionObserver(async () => {
       setIsLoading(true);
       const newComments = await commentWorker.getMoreComments();
       if (newComments.length < LIMIT) {
         setLastPage(true);
       }
       setComments((comments) => [...comments, ...newComments]);
       setIsLoading(false);
     });
     return (
       <StyledCardListContainer>
         {comments.map((comment) => (
           <Card key={comment.id} comment={comment} />
         ))}
         {lastPage && <div>더이상 불러올 데이터가 없습니다...</div>}
         {!lastPage && isLoading && <div>Loading...</div>}
         {!lastPage && !isLoading && <div ref={setObservationTarget}></div>}
       </StyledCardListContainer>
     );
   }
   ```

5. 이제 보이지 않는 `observationTarget` 이 화면에 들어오면 새로운 데이터를 불러옵니다!

### useDebounce

#### 기능

> 검색창을 예로 설명하겠습니다.

1. 검색창에 입력을 넣을 때 마다 검색을 하지 않는다.
2. 입력간의 시간차가 **특이점** 을 넘어서면 등록한 콜백함수 - 검색 을 실행한다.

#### 설계

1. 인자
   1. `debouncing` 될 변수를 받습니다.
   2. `debouncing` 할 시간을 받습니다.
2. 리턴값
   * **디바운스 된 값을 리턴합니다.** 해당 `custom hook` 을 사용한 코드엥서는 이 값에 `effect` 를 걸어서 로직을 실행하면 됩니다.
3. 상태관리
   * `debouncedValue` 를 관리합니다. 해당 값은 정해진 시간 이후에 값이 변하게 됩니다.
4. effect
   * 인자로 들어온 `value` 의 값에 `delay` 만큼 변화가 없다면 `debouncedVaalue` 을 `set` 해줍니다.
   * 해당 기능을 위해 일정시간 뒤에 함수를 실행하게 끔 `setTimeout`을 넣어야합니다. 만약 일정시간 이전에 이벤트가 들어오면 해당 `timeout`을 끝내 줄 필요 또한 있습니다.

#### 구현

> 구현은 너무나 쉽습니다.

```javascript
import { useState, useEffect } from 'react';

function useDebounce({ value, delay }) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);
  return debouncedValue;
}

export default useDebounce;
```

함수가 일정 시간 뒤에 실행될 수 있게 `setTimeout`을 했습니다. 만약 일정시간이 지나기 전에 새로운 이벤트가 들어온다면 그 이벤트를 기준으로 다시 `setTimeout`을 실행해야합니다.

**즉, 이전의 setTimeout은 없어져야만 합니다. 이 때 effect의 clean-up을 활용합니다.**

#### 사용방법

1. `debounced` 할 값과 시간을 정합니다.
   * 입력창에 들어오는 값을 `debouncedValue` 로 합니다.
   * ```javascript
     const debouncedValue = useDebounce({ value, delay: 500 });
     ```
2. `debouncedValue` 가 변하면 실행할 함수를 만듭니다.

   ```javascript
     const debouncedSearch = useCallback(() => {
       handleOnSearch(searchUsersByOption(debouncedValue, selectedOption));
     }, [debouncedValue]);
   ```

3. 끝입니다...ㅎㅎ

### **useDetectOutsideClick**

#### 기능

1. 타켓을 지정한다.
2. 타켓의 외부가 클릭 된다면, 타켓의 `active` 상태를 변화시킨다.

#### 설계

1. 인자
   1. `targetDom` 을 인자로 받는다. 즉 리액트에서는 `ref` 가 된다.
   2. 초기값을 받는다.
2. 리턴값
   * `isActive` 
3. 상태관리
   * `isActive` 
     * 해당 상태는 현재 `target`이 `active`인지 아닌지를 판단하게 해준다.
4. effect
   * 없다.

#### 구현

```javascript
import { useState, useEffect } from 'react';

export const useDetectOutsideClick = (targetRef, initialState) => {
  const [isActive, setIsActive] = useState(initialState);

  useEffect(() => {
    const onClick = ({ target }) => {
      if (targetRef.current !== null && !targetRef.current.contains(target)) {
        setIsActive(!isActive);
      }
    };

    if (isActive) {
      window.addEventListener('click', onClick);
    }

    return () => {
      window.removeEventListener('click', onClick);
    };
  }, [isActive, targetRef]);

  return [isActive, setIsActive];
};
```

#### 사용방법

1. `target` 을 한다.

   * `Dropdown` 을 지정한다.

   ```javascript
   const ref = useRef(null);
   ```

2. `isActive` 값을 어떻게 사용할지 결정한다.

   * 우리는 외부가 눌려지면 드롭다운이 닫히면 되므로 `isActive` === `isOpen` 으로 사용하면 된다.

   ```javascript
   const [isOpened, setIsOpened] = useDetectOutsideClick(ref, false);
   ```

3. 끝....

