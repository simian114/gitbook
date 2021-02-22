# wrap-parameter body가 두 번씩 날라오는 이유

{% hint style="info" %}
\*\*\*\*[**RAILS GUIDE**](https://guides.rubyonrails.org/action_controller_overview.html#json-parameters)\*\*\*\*

\*\*\*\*[**RAILS API**](https://api.rubyonrails.org/v6.1.3/classes/ActionController/ParamsWrapper/Options/ClassMethods.html#method-i-wrap_parameters)\*\*\*\*
{% endhint %}

## 상황

### body로 보낸 parameter 가 두 번 날라온다?

* 레일즈 서버로 data 를 담은채 post 를 보내면,  특정 값은 우리가 감싸지 않았는데도 감싸져 있는걸 확인할 수 있다.
  * 예를 들면 `war_request` 를 승낙할 때 바디에는 `body: { status: "approved" }` 라는 정보만 담긴다.
  * 여기서는 분명히 `war_request` 로 감싸주지 않았다. 하지만 서버에서 `body` 를 출력하면 조금은 이상한 값이 나온다.

![status&#xAC00; war\_request&#xB85C; &#xD55C;&#xBC88; &#xB354; &#xAC10;&#xC2F8;&#xC9C4;&#xB2E4;](../../.gitbook/assets/params%20%281%29.png)

* 바디에 보낸 `status` 가 두 번 나온다. 왜 `war_request` 로 감싸져서 한번 더 나오는걸까?
* 또 재밌는 점은 body로  `body: { status: "approved", test: "test!" }` 와 같이 보내면 `test` 라는 키밸류 값은 `war_request`로 감싸지지 않는다는 사실이다.
  * 여기서 유추해 볼 수 있는건, `status` 는 `war_request` 라는 모델의 속성이고 `test` 는 그 속성이 아니기 때문에 `RAILS` 에서 찰떡같이 처리한다라는 것.

## 해결

### wrap-parameter

* 이 문제\(?\)는 우리도 모르는 사이에 `wrap-parameter` 를 사용하고 있었기 때문이다.
* `wrap_parameter.rb` 파일을 보면 아래처럼 되어있다.

```ruby
ActiveSupport.on_load(:action_controller) do
  wrap_parameters format: [:json]
end
```

* `json` 포맷으로 보내지는 데이터를 `wrap` 하라는 의미가 담겨 있는 것.
* 그렇다면 `wrap`를 한다는건 무슨 말인가? 간단하다 `{ "name": "acme", "address": "123 Carrot Street" }` 와 같은 데이터를 프론트에서 백에 전달했을 때 이 데이터를 처리하는 컨트롤러가 알아서 한번 래핑하는 것. 래핑의 결과는 `{ name: "acme", address: "123 Carrot Street", company: { name: "acme", address: "123 Carrot Street" } }` 와 같다. 즉 프론트에서 손쉽게 데이터를 보낼 수 있게되는 것이다.

