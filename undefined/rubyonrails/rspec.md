# RSPEC으로 모델 테스트하기

{% hint style="info" %}
[**공식홈페이지**](https://rspec.info/)

\*\*\*\*[**RSPEC MATCHERS**](https://relishapp.com/rspec/rspec-expectations/v/2-0/docs/matchers)\*\*\*\*

\*\*\*\*[**RSPEC exception 테스트**](https://relishapp.com/rspec/rspec-expectations/v/3-2/docs/built-in-matchers/raise-error-matcher#expect-no-error-at-all)\*\*\*\*

[**RSPEC RAILS**](https://rspec.info/documentation/4.0/rspec-rails/#What_tests_should_I_write_)
{% endhint %}

## 상황

아래처럼 굉장히 많은 **제약\(validates\)** 가 있는 모델이 있다고 하자

```ruby
class WarRequest < ApplicationRecord
  belongs_to :rule
  has_one :war, dependent: :destroy
  has_many :war_statuses, dependent: :destroy
  has_many :guilds, through: :war_statuses
  validates :status, inclusion: { in: ["pending", "accepted", "canceled"], message: "상태를 잘못 입력하셨습니다." }
  validates :rule_id, inclusion: { in: 1..7, message: "요청하신 룰이 존재하지 않습니다." }
  validates :target_match_score, inclusion: { in: [3, 5, 7, 10], message: "목표 점수를 잘못 입력하셨습니다." }
  validates :max_no_reply_count, inclusion: { in: 3..10, message: "최대 미응답 개수를 잘못 입력하셨습니다." }
  validates :bet_point, inclusion: { in: (1000..10000).step(500), message: "배팅 포인트를 잘못 입력하셨습니다."}
  validates :start_date, presence: { message: "시작일을 입력해주세요."}
  validate :start_date_after_now
  validate :start_date_after_max_end_date
  validate :end_date_after_start_date

  ....
end
```

이 모델을 테스트 하려면 어떻게 할까? 이전까지는 하나하나 .... 일일이 상황을 만들고 브라우저를 키고.. 레일즈 콘솔에 들어가서 하고.... 정말 많은 작업을 통해 테스트를 진행해야 했다.

**RAILS** 는 당연하게도 이런 불편함을 덜어주기 위해 다양한 테스트 방법을 제공한다. 그중에서 우리가 사용할 방법은 **RSPEC**

## 해결 - RSPEC 를 사용하

현재 `war_request.rb` 에는 아래와 같은 클래스 메서드가 있다.

```ruby
def self.create_by(params)
  war_request = self.new(
    rule_id: params[:rule_id],
    bet_point: params[:bet_point],
    start_date: Date.parse(params[:start_date]),
    end_date: Date.parse(params[:start_date]) + params[:war_duration].to_i.days,
    war_time: Time.new(1 ,1 ,1 , params[:war_time].to_i),
    max_no_reply_count: params[:max_no_reply_count],
    include_ladder: params[:include_ladder],
    include_tournament: params[:include_tournament],
    target_match_score: params[:target_match_score],
    )
  if war_request.invalid?
    @error_message = war_request.errors[war_request.errors.attribute_names.first].first
    raise WarRequestError.new(@error_message)
  end
  war_request.save!
  war_request
end
```

뭔가 이상하지 않은가? 그렇다.. 아래를 보면 `war_request` 를 `create` 하는 코드에 `war_statuses` 를 만들어 준다!

왜냐하면 `war_request` 가 만들어지는게 성공한다면 이와 동시에 `war_status` 가 `challenger` 와 `enemy` 양쪽에 생겨야 하기 때문이다.

여기서 문제가 생기는데 `war_request` 의 `valid` 체크를 하기 위해서는 `enemy_guild` 와 `challenger_guild` 의 정보가 필요하지만, 이 두 정보는 `war_request` 의 속성으로 존재하지 않고 `war_status` 의 속성으로 존재한다.

따라서 정상적인 방법으로는 `war_request` 의 `valid` 여부를 확인하지 못하게 되는거고 이 때문에 `create_by` 라는 이상한 클래스 메서드를 만들어서 `war_request` 를 만든다음에 `war_statuse` 를 만들게 된다.

그리고 앞서 말한 `challenger_guild` 와 `enemy_guild` 의 `validate` 검사는 `war_statuse` 에서 하게 된다. `war_statuse` 의 `valid` 체크가 끝나면 그제서야 `war_request` 의 `create_by` 라는 메서드는 종료가 된다...

**위의 과정에서 `valid` 를 통과하지 못하면 `throw` 가 던져지게 된다.**

### throw 가 던져지는 상황을 어떻게 테스트 할까?

자 이제 우리의 `war_request` 모델을 테스트 하기 위해서는 클래스 메서드인 **self.create\_by\(params\)** 를 사용해야 함을 알았다.

그리고 이 `war_request` 가 valid 하지 않다면 **throw** 가 던저지는거 또한 알았다.

따라서 우리가 이 모델을 테스트 하기 위해서는 인위적으로 throw 를 던지는 상황을 만들고, 이를 catch 해서 해당 error 의 class 를 조사해야 하는 과정을 거치면 되는거다! 아마 그 그림은 아래와 같을 것이다.

```ruby
def validation_of_war_request

    begin
    war_request = WarRequest.create_by(params)
    ...
    ...
  rescue SomethingError => e
    ...
  rescue AnotherError => e
    # 이 예외가 발생하면 테스트 성공!!!
  rescue
    ...
  else
    ...
  end 
end
```

흠.. 한눈에 보기에도 아주 힘들어 보인다...

### RSPEC 를 이용한 Exception 테스트

자 이제 RSPEC를 이용해서 진행해 보자.

#### 1. 폴더와 파일을 만든다.

```ruby
# /spec/models/war_request_spec.rb
```

#### 2. 테스트 코드 작성

먼저 상황을 정리하자.

1. 총 11개의 길드가 존재한다.
2. 그 중 4개가 전쟁중이다
   * 전쟁중일 때는 **war-request** 를 만들 수 없다!!
   * **exception 의 던져진다!**

따라서 총 11개의 길드를 **each** 로 돌면서 **만약 해당 길드가 전쟁중이라면 request 를 날리는 테스트를 진행해보자.** 그 결과로 4번의 exception 이 던져질 것이고 우리는 이를 except 하고 있다가 해당 exception 이 던져지면 테스트는 통과 될 것임.

코드는 아래와 같다.

```ruby
Guild.all.each do |guild|
  it "Cannot create war-request which is in-war" do
    p = params.clone
    p[:guild_id] = guild.id
    expect{ WarRequest.create_by!(p) }.to raise_error if guild.in_war?
  end
end
```

