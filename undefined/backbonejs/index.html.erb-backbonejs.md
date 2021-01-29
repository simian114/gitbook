# index.html.erb와 BackboneJS의 결합

## 서버에서 index.html.erb 파일과 JS는 어떻게 결합되고 동작하는가?

* 지금 사용하고 있는 `index.html.erb` 를 보면 온통 `render partial` 로 도배 되어있는걸 확인할 수 있다.
* 이 `render partial` 은 파일의 단위를 모듈별로 나눌수 있게 해준다. 그리고 각 파셜로 `render` 되는 파일들을 살펴보면 대부분이 `script` 태그로 감싸져있는 `underscore template` 파일임을 확인할 수 있다.
* 이 `underscore template` 들은 `backboneJS` 의 `view` 에서 사용할 정보들이다.
* 따라서 서버가 실행되고 클라이언트가 `SPA` 앱에 접속한다면 우리의 서버는 가장 먼저 `index.html.erb` 파일을 클라이언트에게 보내려고 한다. 근데 보내는 `html` 파일\(모든 돔 요소가 생성되기전에\)을 `partial` 부분까지해서 다 보내기 전에 `javascript` 가 개입을 해서 먼저 `script` 파일을 찾으면 어떻게 될까?
  * 당연히 문제가 생긴다.
* 그렇다면 모든 `HTML DOM` 요소가 세팅되기 전에 `BackboneJS` 가 개입하지 않게 하려면 어떻게해야하는가?
* 방법은 간단하다. `jQuery`의 방법을 사용하는것!
* ```javascript
  $(document).ready(function () {
    let MainView = Backbone.View.extend({
      el: "#temp-view",

      initialize: function () {
        console.log("MainView");
        this.current_view = null;
      },
  ...
  ```
* 위 처럼 사용할 `JS`를 **모든 DOM 요소가 준비가 되면 생성하라** 하는 명령을 해주면 된다.

