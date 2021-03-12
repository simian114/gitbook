# 커스텀exception

{% hint style="info" %}
\*\*\*\*[**WHERE SHOULD I PUT CUSTOM EXCEPTION FILE?**](https://stackoverflow.com/a/43924663)\*\*\*\*

\*\*\*\*[**HOW TO MAKE CUSTOM EXCEPTION IN RUBY**](https://www.honeybadger.io/blog/ruby-custom-exceptions/)\*\*\*\*
{% endhint %}

## 상황

valid 체크를 하는 `exception` 을 이용해서 하려고 하는데, 우리가 원하는 메시지를 보내줄 수 없다. 그리고 나는 `raise` 를 할 때 `message` 뿐만 아니라 `status_code` 도 같이 넣어주고 싶다.

어떻게 하면 좋아?

## 해결

### 커스텀 exception 클래스를 만들면 된다.

#### 우선 클래스 위치

[https://stackoverflow.com/a/43924663](https://stackoverflow.com/a/43924663) 의 답변을 보면 `/app/errors/` 폴더를 만들어서 넣으면 된다고 한다.

#### 구현 방법

구현에 앞서 **어떤 exception 클래스를 상속 받을지를 결정해야한다.** 특별한 일 없으면 **StandardError** 을 상속 받으면 된다.

아래의 코드 처럼 작성하자

```ruby
# /app/errors/war_request_error.rb
class WarRequestError < StandardError
  attr_accessor :message, :status_code
  def initialize(message, status_code = 400)
    @status_code = status_code
    @message = message
  end
end
```

코드에 대한 이야기를 하자면, 위에서 말했다시피 `exception` 으로 2개의 정보를 넘겨주기를 원하므로 `initialize` 에 2개의 인자를 넣어준다. 그리고 해당 클래스의 인자로 초기화를 해준다.

여기서 주의할 점은 **attr\_accessor** 를 해준다는 점이다. 어찌보면 당연한건데, 이렇게 클래스가 만들어지고 `new` 를 이용해 만들고 사용할 때 만약 **accessor** 이 구현되어 있지 않다면 해당 값에 접근하지 못한다.\(C++ 의 get, set 을 생각하면됨\)

#### 사용법

```ruby
ActiveRecord::Base.transaction do
  begin
    params = create_params
    guild = Guild.find_by_id(params[:guild_id])
    raise WarRequestError.new("권한이 없습니다.", 403)
  rescue WarRequestError => e
    render_error("전쟁 요청 실패", e.message, e.status_code)
    raise ActiveRecord::Rollback
  end
end
```

`raise` 를 해주고 싶은곳에서 `raise WarRequestError.new( params )` 를 한다. `rescue` 에서는 `WarRequestError` 로 받으면 된다. 또한 이전에 `attr_accessor`를 만들었기 때문에 `get` 해올 수 있다.

