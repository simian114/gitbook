---
description: 콜백메서드는 가볍게 만들자
---

# 콜스택에 있는 블록이 보장된다는 점을 이용해서 브라우저 죽이기

> 이번 실험의 결과에서 주목해야할 점은 **버튼을 누르는 순간부터 화면에 더이상 렌더링이 되지 않는다는것. 렌더링 유무를 확인하기 위해서는 간단하게 버튼에 마우스만 올렸을 때 색이 변하는지 변하지 않는지만 확인하면 된다.**

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

## 버튼을 누르기 전에 예상되는 동작을 설명해보자

1. 코드를 실행하면 `addEventListener` 이 Web api 를 통해 실행된다.
2. 사용자가 버튼을 누르면 브라우저는 이를 알아채고 등록한 `call back` 을 `task queue` 로 넘긴다
3. `event loop` 는 `task queue` 에 멈춰서서 `JS call stack` 을 감시한다. 그리고 `JS call stack` 이 비었다는걸 알게 된 순간 `call back` 을 `JS call stack` 으로 옮긴다.
4. `event loop` 도 마찬가지로 `JS call stack` 을 감시하게 된다.
5. **이제 EVENT LOOP는 CALL STACK에 있는 블록이 끝날 때 까지 블록되어 있는다.**

## 코드가 종료되지 않는다

이전부터 계속 강조했던, 콜스택에 있는 실행블록은 그 실행이 종료됨이 보장된다. 하지만 우리의 코드는 실행이 종료되지 않는다.

이벤트 루프가 계속 블록되어있다는 말의 의미는 브라우저의 렌더링도 되지 않고 다른 큐에있는 메서드들을 실행할 수 없다는 것. 즉 브라우저는 아무것도 못하고 가만히 있다가 죽는수밖에 없다.    

