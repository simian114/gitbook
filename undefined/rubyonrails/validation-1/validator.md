# validator 클래스

{% hint style="info" %}
\*\*\*\*[**RAILS GUILDE**](https://guides.rubyonrails.org/active_record_validations.html#validates-with)\*\*\*\*

\*\*\*\*[**STACKOVERFLOW - validator 파일 위치 1**](https://stackoverflow.com/questions/5263239/where-should-rails-3-custom-validators-be-stored)\*\*\*\*

\*\*\*\*[**STACKOVERFLOW - validator 파일 위치2**](https://stackoverflow.com/questions/42253003/where-should-i-put-the-custom-validators-in-rails-5)

\*\*\*\*[**RAILS API**](https://api.rubyonrails.org/classes/ActiveModel/Validations/ClassMethods.html)\*\*\*\*
{% endhint %}

## 상황

모델에 `validation` 을 작성해주다보면, **기본으로 제공하는 옵션으로 유효성 체크를 하지 못하는 경우가 발생한다.**

이 경우 보통 `validate` 를 사용해서 `custom` 메서드를 만들어서 사용한다. 하지만 이것도 유효성 관련 체크가 너무 길어져서 `model.rb` 파일 자체가 엉망이 되는 경우가 있다.

이럴때는 `validation` 체크를 **`model.rb` 가 아닌 다른 파일에서 해줄주는 없을까?**

## 해결

### validator 클래스를 만들자

[**공식 가이드 페이지**](https://guides.rubyonrails.org/active_record_validations.html#validates-with) 를 보면 자세히 알 수 있다. 우리 코드에서는 `iwoo` 님이 작성한 `tournament` 관련 파일에서 확인할 수 있다.

`validator` 를 사용하기 위해서는 아래와 같은 순서를 따르면 된다.

`validator` 을 작성한다.

* 이 때 `validator` 을 어디에 작성할지 결정해야한다. `model.rb` 또는 다른 파일. 일단 나같은 경우는 `model.rb` 에 너무 많은 커스텀 `validation` 이 들어가서 문제라고 생각했기 때문에 `model.rb` 아닌 다른 파일에 작성해야하는 상황. 이 문제는 아래 링크들을 보고 해결하면 된다.
* [**stackoverflow1**](https://stackoverflow.com/questions/5263239/where-should-rails-3-custom-validators-be-stored)
* [**stackoverflow2**](https://stackoverflow.com/questions/42253003/where-should-i-put-the-custom-validators-in-rails-5)
* 결론은 `validator` 클래스의 정해진 위치는 없지만, `app` 폴더에 `validators` 폴더를 만들고 이 안에 **이름규칙**을 지킨 `validator` 을 만들면 된다. 또한 여기에 만들면 레일즈에서 자동으로 **auto load** 을 해주기 때문에 따로 처리할 일은 없다.

```ruby
# /app/validators/tournament_validator.rb .. 이름 규칙은 다른 파일들처럼 스네이크

class TournamentValidator < ActiveModel::Validator
  def validate(record)
    if record.start_date.nil? || 
        record.start_date.to_date <= Date.current ||
        invalid_tournament_time?(record.tournament_time)
      record.errors.add :base, message: "토너먼트 생성은 내일 이후의 일정으로만 생성 가능합니다."
    end
  end

  def invalid_tournament_time?(tournament_time)
    playble_time = 9..22
    !playble_time.include?(tournament_time.hour)
  end
end
```

`model.rb` 에서 아래처럼 해주면된다! 

여기서 핵심은 _**validates\_with**_ 를 사용한다는 것!!!

```ruby
class Tournament < ApplicationRecord
    ...
  ...
  validates_with TournamentValidator, field: [ :start_date, :tournament_time]
end
```

