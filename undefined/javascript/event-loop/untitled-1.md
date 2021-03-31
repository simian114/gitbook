---
description: Promose로 전달된 call back 은 micro task queue로 전달된다.
---

# Promise 무한반복으로는 브라우저를 죽일 수 있을까?

> 이번 실험의 결과에서 주목해야할 점은 **버튼을 누르면 뭔가 아리까리하게 되는듯 마는듯 하면서 안되는것!!**

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

## 브라우저는 죽는다 대체 setTimeout과 차이점은 뭘까?

> setTimeout에 등록된 callback 은 Task Queue에
>
> promise에 등록된 callback 은 Micro Task Queue에 전달된다.

**Micro Task Queue의 특징은 큐가 비워질때까지 EVENT LOOP가 블록상태에 빠진다는것. 위의 코드는 Promise 의 결과로 Micro task queue에 callback 이 전달되는 속도가 JS Call Stack에서 실행되는 속도보다 빨라서 문제가 브라우저가 죽는것!**    

