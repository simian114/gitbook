# 액션케이블 Consumer를 이용해서 문제 해결

## 문제

로그아웃을 하고 새로 로그인을 해도 새롭게 connection 이 생성되지 않고 이전에 로그인했던 정보가 그대로 `current_user` 로 남아있음

## 액션 케이블 기본 개념

> 문제 해결에 앞서 기본 개념을 다시 정립하자

우리의 서비스를 이용하기 위해서는 반드시 로그인을 해야하므로 _**클라이언트라는 단어는 로그인한 유저를 의미한다.**_ 한명의 사람은 여러 브라우저, 탭, 기기 등을 이용해서 다수의 클라이언트가 될 수 있다. 이런 브라우저 탭, 기기를 앞으로 **환경** 이라 정의하자.

### Connections

_**Connections**_ 이란 client-server의 관계의 기반을 만든다. 하나의 `WebSocket connection` 은 하나의 `connection instance`을 만든다. 한명의 클라이언트는 다수의 브라우저, 탭, 기기들을 이용해서 여러 개의 `connetion` 을 만들 수 있다.

### Consumer

`Websocket`의 클라이언트는 _**consumer**_ 라고 불린다. `Consumer` 는 프론트에서 생성된다.

### Channels

각 Consumer 는 여러 개의 채널을 구독할 수 있다. `Consumer` 는 반드시 하나 이상의 채널을 구독해야한다.

### Subscribers

Consumer 가 채널을 `subscribe` 하면 이 `Consumer` 는 해당 채널의 _**subscriber**_ 가 된다.

#### 정리

**클라이언트**가 되는 순간 웹소켓과 _**Connection**_ 이 이뤄지면서 _**consumer**_ 가 된다.

_**Consumer**_ 는 다수의 _**Channels**_ 를 _**Subscribe**_ 할 수 있다. 해당 _**Channel**_ 의 입장에서는 이런 _**Consumer**_를 **Subscriber** 라고 한다.

_**Consumer**_ 는 반드시 하나 이상의 채널을 구독해야한다.

Rails에서 채널을 생성하고 서버에 접속하면 자동으로 채널을 구독한다. 이 말이 뭐냐면 서버에 접속하는 순간 프론트엔드에서의 클라이언트는 레일즈서버와 `Connect`가 되고 Consumer 가 된다는 것이다. 실제로는 Connect 라는 함수로 연결되는 것이 아닌, 채널을 구독함으로써 웹소켓과 Connection이 이뤄진다.

즉 특정 채널의 구독자가 됨으로써 connection이 이뤄지고 Consumer가 된다는 것.

만약 커넥션이 이뤄지는 순간을 조정하고 싶다면 특정 방법을 사용하면 된다.

Connection 이 이뤄지는 순간 레일즈의 **Connection.rb** 에서는 _**current\_user**_ 라는 객체를 만들고 세팅한다.

프론트의 _**Consumer**_ 가 _**Connection**_ 를 통해 백엔드의 _**current\_user**_ 가 된다고 생각하면 된다. 이제 이 **Consumer와 current\_user** 를 이용해서 백과 프론트를 연동시키면 된다.

Consumer 가 여러 채널을 구독할 수 있으므로 current\_user 는 백엔드에서 여러 채널들과의 상호작용을 담당한다.

## 해결

앞에서 _**Connection**_ 이 이뤄지면 프론트에서는 _**Consumer**_, 백엔드에서는 _**current\_user**_ 가 생성된다고 했다. \(물론 반드시는 아니다. 그냥 우리 프로젝트에서의 이야기임\)

문제를 상기시켜보자면 로그아웃을 하고 다른 아이디로 로그인을 했는데 이전에 로그아웃한 아이디가 망령처럼 남아있는 것이었다.

앞에서의 개념을 다시 생각해보면 문제가 뭔지 알 수 있다. 문제는 바로 _**두번째 로그인했을 때 Connection이 다시 발생하지 않은 것이다.**_

따라서 프론트의 Consumer, 백의 current\_user 의 정보가 그대로 남아버렸다.

> **그렇다면 어떻게하면 로그아웃을 할 때 Connection 자체를 끊어버릴 수 있을까?**

프론트에서 _**Consumer**_ 객체를 직접 이용하면 된다. \(`import consumer from consumer` 으로 `consumer.js`에서 가져오면 된다.\)

**Consumer** 객체를 전역변수로 만들고 브라우저의 콘솔에서 가지고 놀아보자. **Consumer** 를 살펴보면 현재 어떤 채널을 구독하고 있는지도 다 확인할 수 있고, 각 채널에서 사용할 수 있는 메서드도 사용해볼수있다.

