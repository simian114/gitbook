---
description: Router
---

# Part4

## 1.Backbone.Router

* `routers` 를 사용하지 않고도 앱을 만들수있다.
* 하지만 앱에서 특정한 상태\(`state`\) 를 만들고 싶거나, 앱의 특정 위치\(`location`\)으로 이동하고 싶다면 `reference`\(link/ URL\)가 반드시 필요하다.
* ROUTER는 바로 이런 경우에 사용한다.
* 보통의 JS앱은 라우팅을 `hash-tag`를 이용해서 구현한다.
  * `GMAIL`에 접속후 URL을 유심히 살펴보자.
* `backbone`에서는 이 URL 패턴과 함수를 매칭 시킨다.
  * Rasil에서 했던것처럼 인자를 넘길 수 있다.
* 기본적인 구조는 _**`'route' : 'mappedFunction'`**_
  * `'route'` 에 _**\`**_**\`\*** 가 있다면 모든 URL이 여기로 들어온다는 뜻이다.
  * 아래 예시를 보면 _**\`'**_**filter' : 'setFilter'\`\*** 가 있는데
    * `~~#this/is/test/haha` 와 같이 들어오면 \# 뒤에 오는 모든 값들이 인자가 된다.
    * `setFilter` 함수의 인자로 `this/is/test/haha`가 모두 들어가게 된다는 것.

### 1.1 Initializing the Router

* ```javascript
  app.Router = Backbone.Router.extend({
    routes: {
      '*filter' : 'setFilter'
    },
    setFilter: function(params) {
      console.log('app.router.params = ' + params); // just for didactical purposes.
      window.filter = params.trim() || '';
      app.todoList.trigger('reset');
    }
  });

  //--------------
  // Initializers
  //--------------

  app.router = new app.Router();
  Backbone.history.start();
  app.appView = new app.AppView();
  ```

  * 라우터 클래스를 만들고 사용하기 위해서는 먼저 초기화를 시키자
    * 이렇게 한 이후 `URL`의 마지막에 `#This/is/Test` 와 같이 입력을 해보자.
    * 입력된 모든 데이터가 `params`의 형태로 입력되고 콘솔로 출력된다.
  * `setFilter`
    * 이 함수는 최종적으로 `app.todoList.trigger('reset');`를 실행한다
    * `todoList`에 걸어놓은 `event` 인 `addAll` 이 실행된다.
    * 여기서 굳이 왜..? `window.filter` 를 선언하는 걸까?
      * `addAll` 함수에서 사용한다.
      * `fileter` 라는 데이터를 `window` 의 프로퍼티로 만들면 전역함수로 만드는 것과 동일한 효과를 내므로 어디서든 사용할 수 있게 된다.

### 1.2. Processing the routes

* 현재 예시에서는 주소창에 `#아무거나` 로 앱을 호출하면 무조건 `setFilter` 함수가 호출되고 이는 무조건 `addAll`을 호출한다. \(라우팅 주소로 `pending` 과 `completed` 를 보내면 아직은 안된다.\)
* ```javascript
  addAll: function(){
    this.$('#todo-list').html(''); // clean the todo list
    // filter todo item list
    switch(window.filter){
      case 'pending':
        _.each(app.todoList.remaining(), this.addOne);
        break;
      case 'completed':
        _.each(app.todoList.completed(), this.addOne);
        break;
      default:
        app.todoList.each(this.addOne, this);
        break;
    }
  }
  ```
* 주소창 입력으로 들어온 값에 따라 다른 함수가 실행이 된다.
  * 기본적으로는 반복을 돌면서 `li` 태그를 만들어 낸다.
    * 완료된 `todo` 완료되지 않은 `todo` 모두가 출력되는 것.
  * `pending`으로 들어오면 아직 완료되지 않은 `todo` 들만 출력이 된다.
  * `completed`로 들어오면 완료된 `todo` 들만 보인다.

#### remaining\(\), completed\(\)

* 이 두 함수들은 `model`의 `completed` 속성의 `true`, `false` 속성에 따라 값이 결정된다.
* | Completed? | True | False |
  | :--- | :--- | :--- |
  | remaining\(\) | false | true |
  | completed\(\) | true | false |

  즉, _**모델**_ 에 디펜던시가 있다. 이전에 모델을 관리하도록 만든게 `collection`이라고 했다. `collection` 에다가 관련 메서드를 추가해주자.

* ```javascript
  app.TodoList = Backbone.Collection.extend({
    model: app.Todo,
    localStorage: new Store("backbone-todo"),
    completed: function() {
      return this.filter(function( todo ) {
        return todo.get('completed');
      });
    },
    remaining: function() {
      return this.without.apply( this, this.completed() );
    }
  });
  ```

  * `completed`
    * `return` 문의 뒤쪽부터 읽으면 이해하기가 쉽다.
    * ```javascript
      function( todo ) {
        return todo.get('completed');
      };
      ```

      * 이 익명함수는 인자로 들어온 `todo` 모델에서 `completed` 속성을 return 한다.
      * `completed` 속성은 `true` 또는 `false` 둘 중 하나므로 참 거짓이 return 된다.
    * 이제 `this.filter()` 를 살펴보자.
      * 이 부분은 잘 모르겠다!
  * `remaining`
    * `without` 와 `apply` 메서드를 체이닝해서 `completed` 가 false 인 경우를 리턴한다 라고 이해하자...

