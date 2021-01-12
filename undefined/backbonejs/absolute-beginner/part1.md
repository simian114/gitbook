---
description: Backbone은 프레임워크가 아니라 라이브러리다.
---

# Part1

## 1. Brief Background

* 반응형 웹 어플리케이션을 만들기 위해서는 `JS`에 강하게 의존해야한다.
* 초창기의 웹은 정적인 컨텐트 뿐이었다.
* 이후 PHP, Ruby, Java 의 등장으로 사용자의 INPUT, DB의 데이터를 이용해서 웹페이지를 동적으로 만들 수 있게 되었다.
  * 이러한 접근 방식은 현재도 매우 유효하다.
* 하지만 최근들어서는 이전보다 더욱 빠르고 반응이 좋은 웹을 원한다.
  * 이를 위해서는 서버가 아닌 클라이언트 측에서의 작업이 필요한데 이 작업에 특화되어 있는 언어가 바로 `Javascript`
* 따라서 최근의 웹앱은 JS에 강하게 의존한다. 또한 서버사이드의 많은 부분이 `client-side` 로 넘어오게 되었다.
  * JS는 페이지의 reload 없이 특정 부분의 컨텐트를 업데이트 하는게 가능하다.
* 결론은 최근의 웹앱은 `client-side` 의 비중이 높아졌고 이 높아진 비중을 다 흡수해버린게 `JS`
  * 많은 개발자가 JS 개발을 하게 됨에 따라 구조화 되지 않은 부분들 덕분에 코드의 질은 매우 떨어지게 되었다.
* `Backbone`은 구조화 되지 않은 이 더러운 JS 코드를 구조화시키고, 유지보수 가능하게 만들어준다.
  * `Backbone`은 프레임워크이면서 동시에 라이브러라가 된다.

## 2. BackboneJS Overview

* Backbone 은 `underscore.js`에 **hard dependency**를 갖고 `Jquery` 에 **soft dependency**    를 갖는다. 그리고 아래 다섯가지의 모듈로 이루어진다.
  * `Views`
  * `Events`
  * `Models`
  * `Collections`
  * `Routers`

## 3. Hello World in BackBonejs

### 3. 1 Views

* backbone에서의 `View`는 Rails의 `Controllers` 와 같다.
  * User events
  * render html views and templates
  * interact with models
* 아래의 코드는 `hello world`를 출력하는 소스다.
* ```javascript
  var AppView = Backbone.View.extend({
    el: '#hello'
    initialize: function() {
      this.render();
    },
    render: function() {
      this.$el.html("hello world");
    }
  });
  ```

  * 이후에 브라우저의 콘솔에서 `let view = new AppView()` 를 해주자

### 3.2 Backbone's Templates

* [underscore 간단한 예시](https://dololak.tistory.com/406)
* `Backbone` 기본적으로 `underscore`의 템플릿을 사용한다.
* 사용번은 `html` 파일에 `script` 블록 안에 html 문법처럼 사용하면 된다.
  * 만약 JS 변수들을 사용하고 싶다면 `<%= %>`, `<%- %>`, `<% %>` 등을 사용하면 된다.

