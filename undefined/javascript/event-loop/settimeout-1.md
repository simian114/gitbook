---
description: setTimeout의 콜백은 TASK QUEUE에 쌓인다. 결론은 죽지 않는다.
---

# setTimeout 무한반복으로 브라우저는 죽을까?

> 이번 실험의 결과에서 주목해야할 점은 **버튼을 눌러도 브라우저가 죽지 않고 렌더링이 계속 잘 이뤄진다는 것. 렌더링 유무는 마우스를 버튼위에 올려보면 된다.**

```markup
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
5. setTimeout의 콜백이 다시 Task Queue로 들어간다
6. 그럼 이게 또 JS 의 콜백으로 넘어가고
7. ... 반복...
8. **참고로 태스크 큐에서는 하나씩만 JS 콜스택으로 넘어간다.**
9. **이렇게 처리하는 와중에 가끔씩 render에 가서 렌더링도 하고 그런다!!!**

    

{% page-ref page="undefined-2.md" %}

이 실험에서는 버튼을 누르는 순간 **화면의 렌더링이 더이상 진행되지 않았다. 그런데 이번의 실험에서는 어떻게 화면의 렌더링이 잘되는걸까?** 

왜냐면, **setTimeout에 보내진 콜백은 task queue로 넘어가고, event loop 는 단 하나의 콜백만 JS CALL STACK 으로 가져가기 때문. 결국에는**  **event loop 는 순회를 계속하게되므로 render 도 계속할수있는**

