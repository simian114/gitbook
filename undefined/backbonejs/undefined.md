# 뷰 자신이 자신을 지워야 할 때를 감지하려면 어떻게 해야하는가?

## 상황

* 현재 **user\_menu\_view** 는 딱 한개만 만들어지도록 수정하고 있다.
* 여기에서 발생하는 문제는 **user\_menu\_view** 객체가 자기 자신이 필요없어짐을 감지하고 객체 자체를 지워야하는데 자기 자신이 필요가 없어짐을 어떻게 파악하는지에 대한 아이디어가 떠오르지 않고 있었다는 것.

## 해결

* 여기에 IWOO 님이 아주 좋은 아이디어을 생각해주셨는데, 뷰가 아닌 **모델과 컬렉션에** 이벤트\(**listenTo\)**를 걸어두자는 것이었다. 그리고 만약 **user\_menu\_view**가 지워져야 할 경우는 걸어 놓은 리스닝에 의해 걸리고 뷰 자기 자신을 지우면 된다 라고 말씀해주셨다.
* 코드를 기반으로 설명해보자면, 여기에 관련되는 파일과 객체는 아래와 같다.

  * `user.js` 의 `user` 모델
    * 모델 자체에는 아무런 `listenTo` 가 걸려있지 않다.
  * `users.js` 의 `users` 컬렉션
    * 컬렉션 자체에는 아무런 `listenTo` 가 걸려있지 않다.

  > 뷰에서 모델과 컬렉션에 listenTo를 한다. 해당 이벤트 트리거가 일어난다고 해서 절대로 그 모델과 컬렉션에 함수가 구현되어 있는게 아니다. 이벤트가 트리거되면 해당 뷰에서 작업을 수행한다.

  * `user_unit_view.js` 의 `UserUnitView` 뷰
    * `this.model` 에 `create_user_menu` 라는 이벤트가 걸려있다.
      * **create\_user\_menu** 함수를 실행한다.
    * 해당 뷰의 DOM 요소 자체에 `click` 이벤트가 걸려있다.
      * **destroyAndCreateUserMenu** 함수를 실행한다.
  * `user_menu_view.js` 의 `UserMenuView` 뷰
    * `this.parent.online_users` 컬렉션에 `destroy_user_menu_all` 이벤트가 걸려있다. 이벤트 트리거의 결과로 **close** 함수가 실행되고 이 함수는 뷰 자신을 삭제한다.

* 아래는 실행되는 순서다.
  1. 웹 페이지의 **user\_unit\_view** 를 마우스로 클릭한다.
  2. 해당 DOM 요소에 걸려있던 **click event** 가 발동한다.
     1. **destroyAndCreateUserMenu** 가 발동한다. 해당 함수는 컬렉션과 모델 각각의 이벤트를 트리거 해주는 역할만한다.
  3. 컬렉션의 `destroy_user_menu_all` 이벤트를 `trigger` 한다. 이 함수가 실행되면 `user_menu_view` 에 있는 `close` 함수가 실행되면서 기존 메뉴뷰가 삭제된다.
  4. 이후 모델의 `create_user_menu` 를 `trigger` 한다. 그러면 이제 해당 모델의 정보를 이용해서 `user_menu_view` 를 만든다.

