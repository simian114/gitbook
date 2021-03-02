---
description: 너무 오랜만이다...
---

# 커스텀Validation

{% hint style="info" %}
\*\*\*\*[**RAILS VALIDATION GUIDE**](https://guides.rubyonrails.org/active_record_validations.html)\*\*\*\*

\*\*\*\*[**validates vs validate**](https://stackoverflow.com/questions/18140898/whats-the-difference-between-validate-and-validates)\*\*\*\*

\*\*\*\*[**RAILS API ERRORS**](https://api.rubyonrails.org/v6.1.0/classes/ActiveModel/Errors.html#method-i-add)\*\*\*\*

\*\*\*\*[**new, valid?**](https://makandracards.com/makandra/54648-rails-how-to-check-if-a-certain-validation-failed)  ****
{% endhint %}

## 상황

### 모델에서 valid**ation** 검사할 때, 우리만의 validation을 만들고 싶어!

기본적으로 모델에서 validation 을 해주기 위해서는 `validates :rule_id, inclusion: { in: ["pending", "canceled"]}` 처럼 하면 된다. 여기서 주목할 건 **validates**다. 이 **validates** 는 레일즈에서 기본적으로 제공하는 `validation` 이다.

모델 속성이 어딘가에 속하고 그런거라면 **validates** 를 사용하면 된다. 만약 우리가 우리만의 **커스텀 validation**을 만들기 위해서는 어떻게 해야할까?

## 해결

### 바로 _**validates**_ 를 사용하면 된다.

만약 우리의 모델에 `start_date` 와 `end_date` 속성이 있다고 하자. 당연하겠지만 `start_date` 는 `end_date` 보다 미래면 안된다. 이때 `validation` 을 해주는 우리만의 함수를 만들어보자.

```ruby
validate :end_date_after_start_date
...

private
def end_date_after_start_date
  if end_date < start_date
    errors.add(:end_date, "message")
  end
end
```

바로 위의 코드처럼 만들면 된다.

서버에서 `model.create` 또는 `model.save` 를 하게 되면 `model.rb` 파일의 `validation` 들을 적용시키고 `errors`가 단 하나라도 추가 된다면 해당 데이터는 모델의 데이터가 되지 못하고 에러로 끝나게 된다.

[추가적으로 `errors` 에 관한 정보는 여기를 보자](https://api.rubyonrails.org/v6.1.0/classes/ActiveModel/Errors.html#method-i-add)

