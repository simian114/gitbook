# BackboneJS의 각 요소의 역할과 책임을 확실히 이해하자

{% hint style="info" %}
## [Architecture of Backbone 여기를 보자](https://hub.packtpub.com/architecture-backbone/)
{% endhint %}

##  생각

지금 우리의 BackboneJS 코드를 살펴보면, 대부분의 작업을 VIEW 에서 해주고 있다.

링크를 보면 모델, 컬렉션, 뷰 등에 대한 구조와 역할을 제시해주고 있다. 간단하게 요약하자면 **VIEW는 DOM요소를 관리하고 Collection은 Model을 담는 컨테이너의 역할을 해야한다. 그리고 주요 로직들은 Model 에서 수행이 되어야 한다.** 어디서 본것같지 않나..? 그렇다 Rails와 마찬가지다.

그런데 지금 우리의 코드에서는 View 에서 모든걸 다 해주고 있다. Model 은 정작 아무것도 안하고....

## 결론

View 에서는 비즈니스로직을 처리하는 작업을 하지말자.

Collection 은 모델을 담는 컨테이너다. 컬렉션의 역할은 모델의 생성, 삭제, 변경 등의 rest api 의 역할과 `event` 를 트리거 해주는 역할을 담당하면 된다.

Model 에서는 주요 로직이 실행 되어야한다.

