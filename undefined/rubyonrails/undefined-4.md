# 아직 정리하지 못한 것들

## 액션케이블을 이용해서 실시간 통신 정리하기

프론트와 백엔드 간의 데이터 전송이 어떻게 이뤄지는지에 대한 순서를 집중적으로 파고들면 좋아보인다.

또한 백엔드에서는 **connection** 으로 만들어지는 **current\_user** 라는 개념이 존재한다. 이 **current\_user** 의 사용가능 범위도 확실히 알아보고 어떻게 connection 이 이뤄지는지에 대한 것도 알아보면 좋아보인다.

흠.. 그리고 가끔 액션케이블을 이용해서 구현한 **appearance-view** 에 유저가 정상적으로 나오지 않는 경우가 있다. 이유를 확실히 알아보고 문제를 해결하자.

## 액티브 잡 을 실제로 활용해보고 정리해보자

액티브 잡이란 무언가의 일을 레일즈 백그라운드에 맡기는 것. 기본적인 사용법으로는 클라이언트들에게 이메일 자동으로 보내는 것이 있음

우리의 경우 토너먼트를 백그라운드의 액티브잡에서 만들고, 진행하면서 관리한다.

나는 아직 사용해 본적은 없지만 굉장히 중요한 작업으로 생각되므로 확실히 익히도록 하자.

## 웹팩으로 자바스크립트 모듈 관리하기

웹팩이 무엇인지를 먼저 정확히 알 필요가 있다. 이 부분에 대해서는 자바스크립트에서 정리하도록 하자.

우리의 경우 웹팩이 무엇인지 잘 모르고 거기에 더해 레일즈에서 이런 웹팩을 어떻게 관리하는지도 몰랐기 때문에 조금 문제가 있었다.

그런 문제로 인해 `javascript` 폴더 안에 있는 `packs` 에 모든 소스 파일을 넣고 작업을 하고 있었다.

하지만 웹팩이란 **모듈들을 합쳐서 하나로 만드는, 즉 PACKING 하는 것 같다. \(솔직히 잘 모름\)**

### 팩 이란 여러 모듈들이 하나로 합쳐진 녀석이다!

즉, 우리가 프로젝트에서 사용할 여러 모듈들인 `BackboneJS`, `UnderscoreJS`, `jQuery` 등을 한번에 **pack** 으로 관리하기 위해 존재하는 거지 우리의 소스코드들을 하나로 패킹 하기 위해 존재하는 것은 아니다.

아래를 보면 우리의 `packs` 폴더 안에는 **app.js 와 application.js 라는 pack 들이 존재하는 걸 확인할 수 있다.**

```text
# app/javascript
├── channels
│   ├── appearance_channel.js
│   ├── consumer.js
│   ├── direct_chat_channel.js
│   ├── game_channel.js
│   ├── group_chat_channel.js
│   ├── index.js
│   ├── notification_channel.js
│   └── war_channel.js
├── packs
│   ├── app.js
│   └── application.js
└── srcs
    ├── collections
    │   ├── chat_bans.js
    │   ├── chat_messages.js
    │   ├── friends.js
    │   ├── group_chat_members.js
    │   ├── group_chat_messages.js
    │   └── users.js
    ├── draw.js
    ├── dual_helper.js
    ├── helper.js
    ├── internal.js
    ├── models
    ....
```

### 이렇게 packs 폴더에서 packing 된 상태로 존재하는 여러 pack 들을 어떻게 사용하는가?!

`application.html.erb` 나 우리가 만든 `index.html.erb` 파일을보자. 그러면 아래처럼 되어있는걸 확인할 수 있다.

```ruby
# app/views/layouts/application.html.erb
<!DOCTYPE html>
<html>
  <head>
    <title>두근두근 트렌센던스</title>
    <link rel="icon" href="assets/favicon.ico">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>

    <%= stylesheet_link_tag 'application', media: 'all' %>
  </head>

  <body>
    <%= yield %>
  </body>
    <%= javascript_pack_tag 'application' %>
</html>
```

여기서 주목할건 **&lt;%= javascript\_pack\_tag 'application' %&gt;** 위에서 확인했던 **application.js** 를 여기서 사용하는것을 확인할 수 있다!!!

