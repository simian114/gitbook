# preventDefault - passive

{% embed url="https://amati.io/eventlisteneroptions-passive-true/" caption="이 부분은 다음에 정리하자!" %}



## preventDefault

> preventDefault 는 말 그대로 기본 동작을 제한한다.

**preventDefault는 WEB APIs 에 속한다. 즉 브라우저가 갖고 있는 기능이다.**

## 체크박스에서의 preventDefault

{% embed url="https://jsfiddle.net/namssang/y8ewbcdq/10/" %}



체크박스를 클릭하면 아래와 같은 동작이 일어난다.

1. 응? 버튼에 이벤트 핸들러가 등록되어있네? 
2. 나는 **우선적으로 너가 등록한 코드를 먼저 실행할거야!**
3. 등록한 코드가 종료가 되면 그 **다음으로 체크 박스의 기본 동작인 박스에 체크하는 동작을 수행할거야..!**
   * **preventDefault** 가 설정이 되어있구만?!
4. 그렇다면 나는 기본동작을 수행하지 않고 종료할게~

**위의 과정을 거치면서 체크박스에는 체크 박스가 존재하지 않게 된다!**

## 스크롤에서의 preventDefault

{% embed url="https://jsfiddle.net/namssang/y8ewbcdq/14/" %}



스크롤링을 하면 에러가 발생한다??

1. 스크롤링을 한다!
2. 스크롤링에 이벤트가 걸려있네?
3. **체크박스랑 달리, 나는\(브라우저\)**  **브라우저의 스크롤 기본동작인 스크롤링을 먼저 수행할거야!!**
4. 스크롤링이 끝나고... 유저가 등록한 코드를 수행한다
5. 근데 **preventDefault** 가 있네?? 어떻게하지? 나는 이미 기본동작을 수행했는데? 뭐 어떻게하긴 어떻게해... 에러지!

## passive

**체크박스** 에서는 에러가 발생하지 않고 **스크롤** 엥서 에러가 발생했던 이유는 **passive** 여부에 따른 결과다. 이 내용은 다음에 정리하자!

