# 모델은 urlRoot, 컬렉션은 url

## 백본 모델에서는 urlRoot, 컬렉션에서는 url 사용하자!

* [참고](https://riptutorial.com/backbone-js/example/22107/model-urlroot---model-url--)
* 백본 모델은 collection 의 내부에서 사용하는 경우 또는 모델 자체만 사용하는 경우 두 가지로 사용된다. `User` 모델과 이를 기반으로 하는 컬렉션 `Users` 가 있다고 해보자
  1. 컬렉션에서만 사용되는 경우
     * 데이터를 서버에서 가져오기 위해서는`url` 을 설정해주고  `Users.fetch()` 를 하면된다. 
     * 모델은 특별히 `url` 세팅을 해 줄 필요 없다. `url` 세팅같은걸 해줄 필요가 없다는 뜻
  2. 모델만 따로 사용할 경우
     * 이 경우는 생성자로 `id` 또는 `모델`의 일부분이 들어가므로 이 데이터를 이용해서 `fetch` 를 불러와야 하는데, 특이하게 `url` 을 지정할 필요 없이 `urlRoot` 만 해놓으면 인자로 들어간 `id` 에 맞춰서 알맞게 `fetch` 가 된다.
     * ```javascript
       let User = Backbone.Model.extend({
         urlRoot: '/api/users/'
       })
       let user = new User( { id: 1 });
       user.fetch() // /api/users/1 호출
       ```

