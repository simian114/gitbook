---
description: CRUD!
---

# Part3

## 1. Todo item list CRUD

### 1.1. C-reate

-

### 1.2. U-pdate

* 업데이트를 하려면 우선 해당 `todo` 를 더블클릭하자.
  * 더블클릭 이벤트를 등록해야한다.
* CSS
* `TodoView` 클래스에 이벤트를 추가해주자
* ```javascript
  // renders individual todo items list (li)
  app.TodoView = Backbone.View.extend({
    tagName: 'li',
    template: _.template($('#item-template').html()),
    render: function(){
      this.$el.html(this.template(this.model.toJSON()));
      this.input = this.$('.edit');
      return this; // enable chained calls
    },
    initialize: function(){
      this.model.on('change', this.render, this);
    },
    events: {
      'dblclick label' : 'edit',
      'keypress .edit' : 'updateOnEnter',
      'blur .edit' : 'close'
    },
    edit: function(){
      this.$el.addClass('editing');
      this.input.focus();
    },
    close: function(){
      var value = this.input.val().trim();
      if(value) {
        this.model.save({title: value});
      }
      this.$el.removeClass('editing');
    },
    updateOnEnter: function(e){
      if(e.which == 13){
        this.close();
      }
     }
  });
  ```
* `initialize`
  * `view`는 아래 처럼 생성된다.

    ```javascript
    let view = new app.TodoView(model);
    ```

  * 즉 인자로 `model` 이 들어가게 되는데, `initialize` 에서 인자로 들어온 이 `model` 에 `event` 를 걸어준다!
  * 이벤트의 종류는 `change`. 즉 `model` 에 무언가 변화가 있으면 이벤트 트리거가 동작하고 `this.render` 함수가 발동한다.
  * 따라서 `update`를 하면 자동으로 `render` 함수가 호출이 된다!
* `events`
  * ```javascript
    events: {
      'dblclick label' : 'edit',
      'keypress .edit' : 'updateOnEnter',
      'blur .edit' : 'close'
    },
    ```
  * 태그 이름이 `label` 인 DOM 요소를 더블클릭하면 `edit` 가 실행된다.
  * 클래스 이름이 `edit` 인 DOM 요소에서 `키 입력` 을 감지하면 `updateOnEnter` 함수를 실행시킨다.
  * 클래스 이름이 `edit` 인 DOM 요소를 포커싱하고 있다고 포커스를 풀면 `close` 함수가 실행된다.
* `edit`
  * ```javascript
    edit: function(){
      this.$el.addClass('editing');
      this.input.focus();
    },
    ```
  * 현재 수정하고 있는 DOM 요소에 `editing` 클래스를 붙인다.
    * 브라우저에서 element 로 확인하면 동적으로 변화하는걸 알 수 있음.
    * `this.input.focus()` 로 커서를 더블클릭한 요소로 옮긴다.
    * 더블클릭 한 DOM 의 자손에 `input` 이 있는데 이를 선택하고 여기로 `focus`를 옮기는 것으로 추정된다.
* `close`
  * 포커싱을 풀거나 입력이 ENTER 인 경우 실행된다.
  * 포커싱을 풀어도 이전에 수정중이던 데이터는 저장된다.
  * 이후에는 DOM 요소읭 `editing` 이라는 클래스 이름을 지워준다.
* `updateOnEnter`
  * 들어온 키 입력이 ENTER 인지를 판별한다.

### 1.3. D-elete

* k`todo` 아이템을 지우기 위해서는 `remove` 버튼을 각각의 아이템 요소 마다 만들어 주면 된다.
  * 그리고 이 버튼 요소에 `event`를 바인딩 시켜주자.
  * 버튼이므로 버튼의 입력에 대해서 event를 걸면 됨.
* `remove` 버튼은 `li` 태그 각각에 만들어줘야 하니깐 `TodoView` 클래스에 만들어 주면 된다.
  * `<li> ... <button> .... </button> .... </li>`
* 이후에는 `initialize` 에서 모델에 바인딩을 시켜주자.
  * 모델에 바인딩을 시켜주는 이유는 해당 `todo` 가 삭제되면 리스트 목록은 다시 출력되어야 하기 때문!

