# 백본 모델과 컬렉션에서 fetch 를 통해 JSON 가져오기!

## 모델

* [참고1](https://github.com/jashkenas/backbone/issues/4035)
* [참고2](https://stackoverflow.com/questions/18652437/backbone-not-parse-each-model-in-collection-after-fetch/18654257)
* [참고3](https://www.javatpoint.com/backbonejs-model-parse)
* 백본의 모델과 컬렉션에서는 **fetch** 메서드를 통해서 서버로부터 데이터를 가져올 수 있다.
* 모델은 `urlRoot`, 컬렉션은 `url` 을 사용해서 api 주소를 매핑한다.
* 여기서 중요한게 모델을 예로 들어보면

  ```javascript
  let User = Backbone.Model.extend({
    urlRoot: '/api/users'
  })
  let user = new User( { id: 1 }); // id를 1로 하는 user 백본 모델이 생긴다.(아직 이 백본 모델은 id를 제외한 어떠한 정보들도 가지고 있지 않다)
  user.fetch() // /api/users/1 의 url로 서버에게 요청을 보낸다.
  ```

* 위의 `fetch()` 의 결과로 **서버가 `JSON` 을 어떻게 주냐**에 따라 백본에서 해줘야 할 처리가 달라진다.

```text
  // 1. JSON에 특정 객체를 키로 만들어서 주는 방법.
  // 루비에서 render :json => { user: user } 
  {
    "user": {
      "id": 1,
      "name": "sanam",
      "imageUrl": "/~~"
    }
  }


  // 2. 특정 객체가 아닌 그냥 값 주기
  // 루비에서 render :json => user 
  {
      "id": 1,
    "name": "sanam",
    ....
  }
```

* 여기서 만약 **1번** 의 방법을 사용해서 서버가 프론트에 데이터를 전송해준다면, 우리의 `User` 모델은 제대로 초기화 되지 않는다. 왜냐하면 백본 모델은 기본적으로 이미 모든게 파싱된 JSON 을 인자로 받아들여서 모델 자신을 초기화 하는데 **1번** 의 경우에는 **서버가 모든게 파싱된 데이터가 아닌 아직 한겹의 `user` 라는 객체를 `key` 로 하는 JSON 을 줬기 때문이다.** 따라서 특정 방법을 사용해주지 않는다면 **2번** 의 방법으로만 모델을 제대로 초기화할 수 있다.
* 이 문제를 해결할 수 있는 방법이 있는데 바로 `parse` 메서드를 `model` 에 선언해주는것.

  ```javascript
  let User = Backbone.Model.extend({
    urlRoot: '/api/users'
    parse: function() {
        return response.user; // JSON에 user라는 키로 데이터가 전송되기 때문에, user를 키로 하는 value 만을 가져오겠다!
      }
  })
  let user = new User({ id: 1 })
  user.fetch() // 위의 1번 방법으로 서버가 JSON을 보내도 이제 모델을 초기화시킬 수 있다.
  ```

## 컬렉션 \( 문제 발생 \)

* ```ruby
  def index
    users = User.all
    render :json => { users: users}
  end

  def show
    user = User.find_by_id(1)
    render :json => { user: user}
  end
  ```
* ```javascript
  let User = Backbone.Model.extend({
    urlRoot: '/api/users'
    parse: function() {
        return response.user; // JSON에 user라는 키로 데이터가 전송되기 때문에, user를 키로 하는 value 만을 가져오겠다!
      }
  })

  let Users = Backbone.Collection.extend({
    url: '/api/users',
      parse: function() {
        return response.users;
      }
  })
  let users = new User();
  users.fetch()
  window.users = users // 브라우저의 콘솔에서 users를 확인해보자.
  ```
* 서버에서 JSON 을 하나의 객체로 감싸서 프론트에 전송해주고, 프론트는 이제 이 값을 `fetch` 로 받을 때 `parse` 를 통해 한꺼풀 벗겨서 가져온다.
* 코드상으로 분명 문제가 없을거 같지만, 이 코드를 실행하면 정말 엄청난 에러가 생긴다.
* 브라우저에서 `users` 를 분석해보자. 그러면 이 `users`가 모델의 배열이 아닌, **자시 자신의 배열로 무한 참조가 된다는 것을 확인할 수 있다.** 엄청난 문제다...

## 문제 해결

* 이렇게 된 원인은 `parse` 메서드에 존재한다. \(솔직히 정확한 원인은 모른다. 머리가 아프다. 자고 싶다\)
* 해결법은 아래처럼 `model` 에서의 `parse` 를 수정해주면 된다.
* ```javascript
  let User = Backbone.Model.extend({
    urlRoot: '/api/users'
    parse: function() {
        if (options.collection) {
        return response;
      } else {
            return response.user;      
      }
      }
  })

  let Users = Backbone.Collection.extend({
    url: '/api/users',
      parse: function() {
        return response.users;
      }
  })
  ```
* 
