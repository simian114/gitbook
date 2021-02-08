# 20210208\(월\)

### 1. 학습 날짜

* 2020-02-08

### 2. 학습 시간

* 11:00 ~ 24:00

### 3. 학습 범위 및 주제

* 백본 view, model, collection

### 4. 학습 목표

* appearance view 구현 완료하기

### 5. 학습 정리

* [window 이벤트를 listenTo 로 감시하게 만들기](https://simian114.gitbook.io/blog/undefined/backbonejs/window-listento)
* [BackboneJS의 각 요소의 역할과 책임](https://simian114.gitbook.io/blog/undefined/backbonejs/backbonejs)

### 6. 상세 학습 내용

* 현재 `user_menu_view` 는 메뉴뷰가 켜진 상태에서 브라우저의 사이즈를 조절하면 문제가 생겼다. 이에대한 해결책으로 여러 아이디어가 나왔지만 그 중 가장 구현이 용이하고 모든 사람이 동의할만한 아이디어는 브라우저의 사이즈가 조절될 때 메뉴뷰를 없애면 된다는 것이었다.
* 문제는 window - resize 라는 이벤트를 감시하기 위해서는 backboneJS 의 events 또는 listenTo를 사용하지 못한다는 것이었다.
* 그렇다고 해서 jQuery 를 사용해서 이벤트를 바인딩하면 정말 꼴보기 싫은 코드가 만들어진다.
* 따라서 이 부분을 어떻게 할지에 대해 알아봤고 그에 대한 해결책으로는 underscoreJS 와 BackboneJS를 조화롭게 활용하는 방법이 있었다.
* 먼저 `extend` 라는 underscore 문법을 사용해서 window 객체에 Backbone.Events 를 넣는다.
* 이렇게 되면 앞으로는 view 에서 window 를 직접 listenTo 할 수 있게 된다!
* BackboneJS 의 각 요소의 역할을 정리해보자.
  * 현재 우리의 코드를 보면 모든 작업들이 view 에서 수행되고 있다.
  * 하지만 VIEW는 DOM 요소의 작업과 event 에 대한 작업만을 수행하지 비즈니스 로직을 수행하는 곳이 아니다.
  * 따라서 그런 비즈니스 로직을 담당하는 부분은 model로 옮긴다. 그리고 collections 또한 수정해서, collection 은 특정 모델을 trigger 하거나 또는 생성 수정 삭제 등의 rest api 를 담당하게 한다.

### 7. 오늘 학습 내용에 대한 개인적인 총평

* 흠... 코드를 작성하는 시간 절반이었고 나머지는 전부 회의 또는 PR 를 확인하는 시간이었다. 하지만 아직도 PR에 대한 의견을 적지를 못했다ㅋㅋ 참나..

### 8. 다음 학습 계획

* appearance view 완성하기

