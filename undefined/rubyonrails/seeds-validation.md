# seeds 데이터 validation 스킵

{% hint style="info" %}
\*\*\*\*[**모든건 STACKOVERFLOW에...**](https://stackoverflow.com/questions/15848952/turn-off-validation-in-seeds-rb)\*\*\*\*
{% endhint %}

## 상황

War 관련 작업을 하는 가운데 제대로 동작하는지를 확인하기 위해서는 `war` 와 관련된 몇개의 모델을 만들고 테스트 해야만 했다. **문제는 관련된 모델을 seeds 에서 create 로 생성하면 VALID 에 걸려서 에러가 난다는 것.**

예를 들면 **war\_request** 가 그런 상황인데, **create** 가 될 때 마다 체크하는 **WarRequestCreateValidator** 을 보자

```ruby
class WarRequestCreateValidator < ActiveModel::Validator
  def validate(record)
    @start_date = record.start_date.to_date
    @end_date = record.end_date.to_date
    @war_time = record.war_time
    errors = record.errors

    if @start_date.nil? || @end_date.nil?
      errors.add(:base, "전쟁 시작일을 입력해주세요")
    elsif start_date_after_max_start_date?
      errors.add(:base, "전쟁 시작일은 60일 이내로 설정해야 합니다.")
    elsif start_date_after_tomorrow?
      errors.add(:base, "전쟁 시작일은 내일 이후여야 합니다.") # 여기가 문제다!!!
    elsif end_date_after_start_date?
      errors.add(:base, "전쟁 종료일은 시작일 이후여야 합니다.")
    elsif end_date_after_max_end_date?
      errors.add(:base, "전쟁 종료일은 시작일 기준 7일 이내여야 합니다.")
    elsif invalid_war_time?
      errors.add(:base, "전쟁 시작 시각은 9시부터 22시 사이여야 합니다.")
    end
  end
...
  end
```

**war** 를 테스트 하기 위해서는 **반드시 war\_request 의 start\_date를 과거로 만들어야한다.** 하지만... 과거로 만들면 validator에 걸려 버리는걸 ㅜ

## 해결

지금까지 **seeds** 파일에서는 **create**만 사용해서 레코드를 생성하고 있었다. 이런 편견에서 벗어나서 **new**와 **save**를 이용하면 된다.

[stackoverflow](https://stackoverflow.com/questions/15848952/turn-off-validation-in-seeds-rb) 를 보면 힌트를 얻을 수 있다.

_**save 메서드에는 validate 체크 여부를 옵션으로 건네줄 수 있다!!!**_ 이를 활용해서 다시 시드를 만들어 보자. 하지만 여기서 또 문제가 발생한다. 기존의 create 를 사용할 때는 아래처럼 배열로 묶어서 한번에 처리해줬지만, **new, save** 를 활용하면 이런 하나하나 해줘야 하는 상황이 발생한다. 이때는 아래처럼 하면 된다. 배열로 만들고 each 를 사용하면됨.

```ruby
War.create([
  {war_request_id: 1, status: "progress"},
  {war_request_id: 10, status: "completed"},
  {war_request_id: 11, status: "completed"},
  {war_request_id: 12, status: "completed"},
])

# new와 save 를 사용
war_request_list = [
  {rule_id: 2, bet_point: 100, start_date: ...},
  {rule_id: 3, bet_point: 100, start_date: ...}
  {rule_id: 4, bet_point: 100, start_date: ...}
  ...
  ]
war_request_list.each do |war_request|
  WarRequest.new(war_request).save(validate: false)
end
```

