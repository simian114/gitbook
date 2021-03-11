# Backbone Model 프로토타입에 메서드 구현하기

## 상황

프론트에서는 Backbone 의 모델을 이용해서 여러 작업을 한다. 지금 user 모델을 이용해서 특정 작업을 해야하는데, **user 모델을 비교해서 본인인지 타인인지를 구분해야한다.**

물론 아주 간단하게 각 모델의 `attribute` 를 비교할수도 있지만, 조금 멋있는 방법을 사용해보자.

## 해결

### Backbone.model 프로토타입에 메서드 구현하기

백본의 모든 모델은 아래처럼 선언된다.

```javascript
let CurrentUser = Backbone.Model.extend({
  ...
})
```

여기서 알 수 있는건 **Backbone.Model** 을 상속한다는 것. 따라서 **Backbone.Model** 에 메서드를 구현해 놓으면 이를 상속받는 자식들은 해당 메서드를 특별히 구현하지 않아도 기본으로 장착하는 상태가 된다.

### 어디다가 어떻게..?

아래처럼 Backbone Model 의 prototype 에 **equalTo** 메서드를 만들어 놓으면 이후부터는 모든 모델에서 **equalTo** 메서드를 사용할수있다!!!

```javascript
// /app/javascript/srcs/config/backbone.config.js
Backbone.Model.prototype.equalTo = function (other) {
  if (!other) return false;
  return _.isEqual(this.id, other.id);
};
```

이후부터는 아래처럼 모델이라면 언제든지 사용할 수 있게 된다.

```javascript
// /app/javascript/srcs/views/appearance/friends_list_view.js
addOne: function (user) {
  if (App.current_user.equalTo(user)) return;
```

