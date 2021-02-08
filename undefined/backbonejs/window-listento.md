# Window 이벤트를 listenTo로 감시하기

## 상황

`user_menu_view` 를 켜놓은 상태에서 브라우저의 크기를 조절하면 `menu`의 위치는 처음 생성된 위치 그대로 고정이 되기 때문에 반응형으로 움직이지 못해서 보기에 매우 안좋다.

따라서 이 방법을 해결하기 위한 방법으로는 아래와 같은 아이디어가 있었다.

1. `user_menu_view`를 `user_unit` 의 내부에 만들어서 자연스럽게 브라우저의 크기에 맞춰서 렌더링하게 만들기
   * 이 방법으로 처음에 구현했었지만 메뉴뷰가 `appearance view` 자체에 속하게 되면서 오히려 보기 안좋아졌었다. css에 능통하다면 해결할 수 있었을거 같지만, 더 좋은 아이디어를 팀원분이 내주셔서 이 의견은 기각
2. 브라우저의 크기를 조절하는 순간 `user_menu_view` 를 `remove` 하는 아이디어
   * 이 아이디어를 채택!

## 해결

브라우저의 크기를 `resize` 에 대해 이벤트를 구독해두고 만약 발동하면 지우면 된다.

여기서 문제가 생겼다. 기존에는 뷰의 **events** 를 이용해서 DOM 에 이벤트를 등록하거나 또는 **listen** 를 이용해서 모델이나 컬렉션에 이벤트를 등록했다.

그런데 지금 이벤트를 등록해야 하는건 **window!!!**

이에 대한 해결법은 [stackoverflow](https://stackoverflow.com/questions/9110060/how-do-i-add-a-resize-event-to-the-window-in-a-view-using-backbone/9110145) 에 있었고 2가지의 방법이 있었다.

1. 제이쿼리의 **bind** 를 사용하는 방법
2. **underscorejs** 의 **\_extends** 을 이용해서 **listenTo** 를 사용하는 방법

당연히 두 번째 방법을 이용해서 구현하면 된다.

### underscoreJS의 extend 를 이용해서 이벤트 등록하기

[stackoverflow](https://stackoverflow.com/questions/9110060/how-do-i-add-a-resize-event-to-the-window-in-a-view-using-backbone/9110145) 의 아래를 보면 `_.extend(window, Backone.Events)` 구문이 존재한다. 이 구문을 해석해면" `window` 에 `Backbone.Events` 를 얕에 복사해서 넣는다" 이다. 즉 이제부터 `window` 을 이용해서 `Backbone.Events` 의 기능을 사용할 수 있다는 것.

이후에는 `view` 에서 `listenTo`를 걸어주면 된다 :\)

