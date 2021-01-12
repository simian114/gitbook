---
description: 'Models, Collection and Views'
---

# Part2

## 1. Todo Ap in Backbone

* 이 듀토리얼을 모두 끝내고 나면 `Todo` 웹앱이 완성된다.

## 2. Backbone.Model

* model은 앱의 심장이다.
* model은 `interactive data` 와 로직을 포함한다. 또한 데이터의 검증, `setter` 와 `getter` 그리고 `default values`, `data initialization`, `conversion` 등 정말 많은 일을 한다.
* `Todo`라는 이름의 모델을 만들어보자.
* ```javascript
  var app = {}; // create namespace for our app
  app.Todo = Backbone.Model.extend({
    defaults: {                // 기본값
      title: '',
      completed: false
    }
  });
  ```
* 콘솔에서 가지고 놀아보자!
  * ```javascript
    var todo = new app.Todo({title: "test!"});
    todo.attributes; // 모든 속성 출력
    todo.get('title'); // title 속성 출력
    todo.get('complated_at')
    todo.get('time') // undefined
    todo.set('time', Date()) // 현재 시간으로 time 속성이 생김
    ```

## 3. Backbone.Collection

* `collection`은 `models`의 집합이다.
* `models`를 `collections` 에서 관리함으로써 보다 효율적으로 `event` 등의 변화를 감지할 수 있고 데이터를 `fetching` 하는것이 가능해진다.
* `collection`은 데이터를 다루는 작업을 한다. 따라서 DB와 연동할 수 있는 무언가가 필요하다!
  * `URL`!!! 속성 또는 함수가 무조건 있어야한다.
* ```javascript
  app.TodoList = Backbone.Collection.extend({
    model: app.Todo,
    localStorage: new Store("backbone-todo")
  });
  ```

  * 위의 코드에는 `URL` 없는데?
    * 지금은 백엔드와 연동하는 예시가 아니므로 웹 자체에 데이터를 저장해야한다. 따라서 이런 편법을 사용
* `part1` 에서 `Model` 를 만들고 이를 이용해서 `todo` 객체들을 생성했었다.
  * 이렇게 만들어진 객체들을 `collection` 에 `add` 할 수 도 있고
  * 또는 `collection` 자체에서 모델을 `create` 할 수도 있다!
* ```javascript
  let todoList = new app.TodoList();

  todoList.create({title: "created by Collection!"});

  let todo = new app.Todo({title: "add!"});
  todoList.add(todo);

  // pluck 메서드는 underscorejs에서 작성된 메서드다.
  todoList.pluck('title') // ['created by ~', 'add!'];
  ```

## 4. Backbone.View

* `Views`는 HTML 마크업 파일을 가지고 있지 않다. 대신 동적으로 변화할 수 있는 데이터와 템플릿에 대한 링크를 가지고 있다. 그리고 이벤트 또는 데이터의 변화가 올 때 템플릿 파일을 렌더링해서 HTML을 만들어 낸다.

### 4.1 Basic Properties

> view의 4가지 기본 속성 el, initialize, render, events 를 알아보자

#### 4.1.1el

* 모든 `view`는 DPM 요소에 대한 레퍼런스를 무조건 가지고 있어야 한다.
  * 이 reference를 이용해서 컨텐츠를 삽입한다.
* 이러한 동작을 하는게 바로 `el` 프로퍼티
* `el` 프로퍼티는 템플릿에 이미 존재하는 DOM일수도 있고 아닐수도 있다.
  * 만약 존재하는거면 이 요소를 찾는 작업이 필요하다.
    * `el: 특정 요소 찾기`
  * 없다면 태그네임, 클래스 등 속성을 만들어 줘야한다.
    * `tagName`: 새로 만들 요소의 태그 이름
      * 지정해주지 않는다면 자동으로 `div`로 설정된다.
    * `className`: 클래스 이름
    * `id`: id 값

#### 4.1.2 Initialize / Constructor

* ...

#### 4.1.3 render

* `render`는 `markup`을 `element`에 삽입해준다!
* 모든 `view`가 `render` 함수가 필요한건 아니다.

#### 4.1.4 delegated events

* event는 아래와 같은 형식으로 쓰여진다.
  * `{"<EVENT_TYPE> <ELEMENT_ID>": "<CALLBACK_FUNTION>"}`
* 예시
  * `events: {'keypress #new-todo': 'createTodoOnEnter'}`
  * 키가 눌리는걸 감시하고, 만약 키가 눌렸다면 `new-todo` id 를 가진 속성에 `createTodo~` 함수를 적용한다.

## 5. Todo View

* 우선 예시를 보자
* ```javascript
  <script type="text/template" id="item-template">
    <div class="view">
      <input class="toggle" type="checkbox">
      <label><%- title %></label>
    </div>
  </script>
  ```
* ```javascript
  // renders individual todo items list (li)
  app.TodoView = Backbone.View.extend({
    tagName: 'li',
    template: _.template($('#item-template').html()),
    render: function(){
      this.$el.html(this.template(this.model.toJSON()));
      return this; // enable chained calls
    }
  });
  ```
* 첫 번째 코드는 `underscoreJS`로 만든 템플릿이다.
  * `script`의 타입속성과 id 속성에 주목하자.
    * `type` 속성은 지금 이 스크립트 내용은 이후에 HTML 마크업이 된다는 걸 뜻한다.
    * `id속성` 으로 특정 `view` 에서 템플릿을 지정한다.
* 두 번째 코드

  * `<li>` 태그를 만든다.
  * 이 `view` 가 사용하는 템플릿의 `id` 는 `item-template` 다
  * `render` 함수를 보자 `this.$el` 은 위에서 생성한 `li` 태그를 의미한다.
    * `this.$el.html`은 아래의 `here` 부분에 값을 넣는다
      * ```markup
        <li>here!!!</li>
        ```
  * `this.template(this.model.toJSON())`
    * 여기를 보면 템플릿에 `this.model` 을 넣어준다.
    * 이 `this.model` 은 도대체 어디서 왔을까?
      * `let view = new app.TodoView({model: todo});`
      * 이렇게 `view`를 생성할 때 인자로 넘겨준 `model`이 `this.model` 이 된다라고 알고 진행해보자.
    * 이제 첫 번째 코드를 보면 `<%- title %>` 가 있다. `model.toJSON())`으로 보내준게 `{title: "title!", completed: false}` 이렇게 전송이 됐고, 템플릿에서 이 `title`를 사용한것.

  **6. Backbone.Events**

  * 이벤트 모듈은 어느 객체와도 믹스인 되어서 사용할 수 있다.
  * `on`, `off`, `trigger` 등을 이용해서 이벤트를 사용해보자

  **6.1 이벤트 등록하기**

  * `object.on(event, callback, [context])`
    * `bind` 라고 불린다.
    * `on` 은 객체를 이벤트와 콜백함수에 바인딩한다.
    * 만약 바인딩된 이벤트가 걸리면 콜백함수가 실행된다.
  * 예시를 하나 만들어보자.
    * ```javascript
      let obj = {};
      let callback = function(msg) { console.log(msg); };

      // error! 먼저 믹스인 시켜야한다!
      // obj.on("event", callback); 

      _.extend(obj, Backbone.Events) // 이제 일반 객체도 이벤트를 등록할 수 있게되었다.
      obj.on("event", callback); // 이벤트 등록
      obj.trigger("event", "my custom event!"); // 트리거 발동
      ```

  **7. App View**

  * 지금까지 학습했던 `View`로 `TodoView`를 만들어왔다.
    * `TodoView`의 객체는 하나의 `li` 태그를 만들고 이를 렌더링한다.
    * 생각하면 `todo` 앱은 하나가 아닌 복수의 todo 카드가 존재해야한다.\(복수의 li 태그가 있어야 한다\)
    * 이말은 즉 여러개의 `TodoView` 객체가 있어야 한다는 것이다.
    * 그러면 손수 이 객체들을 만들어야하나?
      * 아니다!
    * **`TodoView` 객체를 관리하는 더 큰 범주의 `View` 를 만들자.**

  ```javascript
  app.AppView = Backbone.View.extend({
    el: '#todoapp',
    initialize: function () {
      this.input = this.$('#new-todo');
      // instance of the Collection
      app.todoList = new app.TodoList();
      // when new elements are added to the collection render then with addOne
      app.todoList.on('add', this.addOne, this);
      app.todoList.on('reset', this.addAll, this);
      app.todoList.fetch(); // Loads list from local storage
    },
    events: {
      'keypress #new-todo': 'createTodoOnEnter'
    },
    createTodoOnEnter: function(e){
      if ( e.which !== 13 || !this.input.val().trim() ) { // ENTER_KEY = 13
        return;
      }
      app.todoList.create(this.newAttributes());
      this.input.val(''); // clean input box
    },
    addOne: function(todo){
      var view = new app.TodoView({model: todo});
      $('#todo-list').append(view.render().el);
    },
    addAll: function(){
      this.$('#todo-list').html(''); // clean the todo list
      app.todoList.each(this.addOne, this);
    },
    newAttributes: function(){
      return {
        title: this.input.val().trim(),
        completed: false
      }
    }
  });
  ```

  * `initialize`
    * `this.input = this.$('#new-todo');`
      * `appView`의 프로퍼티로 `input`을 만든다.
      * `input`은 HTML에서 `id` 값이 `new-todo`인 DOM이다.
    * `app.todoList = new app.TodoList()`
      * collection
    * `app.todoList.on('add', this.addOne, this)`
      * `todoList`에 새로운 모델이 더해지면 `this.addOne`가 발동한다.
      * 세 번째 인자 `this`는 컨텍스트를 알려준다.
      * 여기서는 굳이 사용할 필요가 없다.
    * `app.todoList.on('reset', this.addAll, this)`
      * `reset` 되면 모든 리스트를 다시 띄운다.
  * `events`
    * `#new-todo` 인 DOM 에 대해서 `createTodoOnEnter` 함수를 실행시켜라
  * `createTodoOnEnter`
    * 인자로 `e`가 들어온다. 이 `e` 는 `input`으로 들어온 값이다.
    * 입력된 값이 `Enter`면 입력된 데이터를 기반으로 새로운 모델을 만든다.
    * 이후 `input` 박스의 값을 공백으로 수정한다.
  * `addOne`
    * Todo 가 하나 늘어난다는 것은, 리스트에 하나가 추가됨을 의미한다.
      * 리스트에 하나가 추가 된다는 것은 `li` 태그가 하나 더 생긴다는것
  * `addAll`
    * 이니셜라이즈 함수에서 이벤트를 `on` 할 때를 보면 세 번째 인자로 `this` 를 넣어주는걸 볼수있다.
    * 즉, 이 함수 안에서 사용되는 `this`의 주체는 `addAll` 함수 자체가 아닌 `initialize` 다.
      * `initialze`가 아니라 `appView` 인가..?
  * `newAttributes`
    * `{ }` 객체 스타일로 리턴한다. 이 값으로 모델을 초기화한다.

