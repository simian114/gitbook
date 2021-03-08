# 모델이름바꿀때명심할것

* 마이그레이션 파일을 만들어서 레일즈의 DB 테이블 이름을 수정하면 끝날거 같지?
* 아니다.... `app > model` 에서 선언한 **연관관계** 에 사용된 모든 테이블 이름을 수정해야 한다..
* 모델의 DB 테이블 이름을 수정할 때, 마이그레이션 파일만 만들어서 하는게 아니라 레일즈의 Model 에서 연관으로 사용하고 있는 모든 이름을 다 바꿔줘야한다. 예를 들어 `DirectChatBan` 테이블 이름을 `ChatBan` 으로 바꾼다면 마이그레이션 파일 뿐만 아니라 이 모델을 연관으로 갖고 있는 `Users` 모델에서도 `has_many :direct_chat_ban` 을 `has_many :chat_ban` 으로 바꿔줘야 함을 의미한다.
