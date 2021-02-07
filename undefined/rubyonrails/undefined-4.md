# 액션케이블 연결 순서

* 현재 로그인을 하면 바로 채널을 subscribe 한다.
* 채널을 subscribe 하는 순간 만약 현재 유저가 아무 체널도 구독하고 있지 않았다면 아래의 동작을 수행한다.\( 만약 서버와 프론트 간의 **connection** 이 존재하면 아래 과정은 생략된다.\)
  * 서버와 **connection** 이 되어 있지 않았다면 이를 실행한다.
  * 서버의 `connection.rb` 에서 **connect** 함수를 호출하고 이는 서버와 프론트의 연결을 만든다. 이 함수로 인해 프론트에는 **consumer** 가 생기고 백에서는 해당 **consumer** 를 구분하기 위해 **current\_user** 라는걸 만든다.
  * 그리고 프론트의 `appearance_channel.js` 에서 **connected** 함수를 호출함으로써 서버와 프론트간에 **connection** 이 생겼음을 알려준다. 이 커넥션이 생기는 순간 프론트에 **consumer** 가 생기는 것임.
* 아무튼 위에서 **subscribe** 를 결국 한거고 **subscribe** 를 하는 순간 `appearance_channel.rb` 의 **subscribed** 함수가 호출된다.
  * **subscribed** 함수 내부에서는 `current_user` 를 확인하고 에러가 없으면 **User 모델** 의 **notice\_login** 이라는 함수를 호출한다.
* `User.rb` 파일에 가보면 비즈니스로직들이 정의되어 있는데 여기서 `notice_login` 을 살펴보자.
  * 함수를 보면 **user\_data** 라는 객체를 만들어서 프론트에 전달해줄 정보를 담는다.
  * 그리고 **ActionCable.server.broadcast** 를 이용해서 해당 채널을 구독하고 있는 모든 구독자에게 누군가 접속을 했다고 알려준다.

