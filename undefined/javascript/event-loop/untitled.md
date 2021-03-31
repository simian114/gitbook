---
description: RAF는 Request Animation Frame 의 약자. 렌더링 되기전에 수행되어야 하는 콜백을 지정하는 Web API 다.
---

# RAF는 그럼 뭐야?

{% embed url="https://www.youtube.com/watch?v=9XnqDSabFjM" %}



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

이 RAF 는 실제로 굉장히 많이 수행된다!!!

