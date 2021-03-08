# validation 검사가 save, update, create 모든 경우에 일어난다

{% hint style="info" %}
\*\*\*\*[**RAILS VALIDATOIN GUILD ON OPTION**](https://guides.rubyonrails.org/active_record_validations.html#on)\*\*\*\*
{% endhint %}

## 상황

기존에 **war\_request** 를 보면 **war\_request\_validator** 를 만들어서 **new, save** 할 때 마다 **valid** 체크를 하게 만들었다.

하지만 여기서 문제가 발생했다. 바로 **update** 할 때도 **valid** 체크가 이뤄진다는 것!!!

아주 간단한 해결법이있다.

## 해결

[**Rails validation guide \:on**](https://guides.rubyonrails.org/active_record_validations.html#on) 을 보면 바로 해결할 수 있다.

**on** 옵션을 주면 내가 원하는 상황에서 **valid** 검사를 할 수 있게 만들어준다.

따라서 위와 같은 상황에서는 **valid** 체크를 **create** 인 경우에만 해주면 되므로 아래처럼 수정해주자.

```ruby
validates_with WarRequestCreateValidator, field: [ :start_date, :end_date, :war_time ], on: :create
```

