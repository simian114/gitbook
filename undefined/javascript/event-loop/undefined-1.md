# 이벤트 루프의 동작

event loop 는 크게 아래 4부분을 돌면서 감시한다. 즉 논블록킹 상태로 아래를 감시하는것.

1. JS CALL STACK
2. RENDER
3. TASK QUEUE
4. MICRO QUEUE

## 이벤트루프가 JS CALL BACK에 있을때

JS CALLBACK 에 콜백 메서드가 존재하면 **이벤트 루프는 콜백이 비워질때까지 블록된 상태로 있는다.**

## RENDER에 있을 때

화면을 새롭게 렌더링한다. 브라우저는 60FPS 를 유지하려는 특징이 있다. 60FPS 를 유지하기 위해서는 **16.7ms** 의 속도로 한번씩 렌더링을 해줘야한다.

그런데 **EVENT LOOP가 논블록킹 상태로 감시영역을 도는 속도는 보통 1ms 정도 된다. 즉, RENDER 에 무언가 할 일이 있더라도 EVENT LOOP 는 RENDER 를 바로 하지 않고 타이밍을 재가면서 render을 한다는것**

만약 사용자가 **requestAnimationFrame 메서드를 사용한다면 이 메서드들은 render 의 가장 앞부분에 있는 큐에 들어가게되고 EVENT LOOP가 RENDER로 왔을 때 가장먼저 차례로 수행된다.**

## TASK QUEUE

여기에 정말 아무리 많은 콜백이 쌓여있다고 해도 **단 하나의 콜백만 JS의 콜스택으로 옮긴다. 그리고 EVENT LOOP 자신도 JS CALLBACK으로 넘어간다.** setTimeout 등에 들어온 콜백들이 여기로 온다.

## MICRO QUEUE

만약 해당 큐에 수많은 콜백이 있다면 **EVENT LOOP는 모든 콜백을 처리할 때 까지 움직이지 않는다. 즉 블록상태에 빠진다.** Promise, Observer 등에 등록된 콜백이 여기로 들어온다.

