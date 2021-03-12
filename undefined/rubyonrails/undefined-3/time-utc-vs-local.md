# TIME ZONE 설정하기

## 상황

우리의 서비스는 날짜와 시간을 기반으로 특정 이벤트가 발생해야한다. \(예를 들면 토너먼트, 길드 전쟁\) 하지만 `Time.now` 등과 같이 현재 시간을 찍어보면 **이상한 시간이 찍힌다!!!**

## 해결

### UTC? Local?

이 문제는 **시간을 가져오는 베이스가 한국이 아니라 UTC로 되어있었기 때문이다.** 따라서 **이 베이스를 한국으로 바꿔주면 된다.**

```ruby
# /config/application.rb

require "rails/all"

# Require the gems listed in Gemfile, including any gems
# you've limited to :test, :development, or :production.
Bundler.require(*Rails.groups)

module Project
  class Application < Rails::Application
    # Initialize configuration defaults for originally generated Rails version.
    config.load_defaults 6.1

    # Configuration for the application, engines, and railties goes here.
    #
    # These settings can be overridden in specific environments using the files
    # in config/environments, which are processed later.
    #
    config.time_zone = "Seoul" # 원래 이 부분이 USA & CANADA 이렇게 되어있음
    # config.eager_load_paths << Rails.root.join("extras")
  end
end
```

### BASE 를 설정해도 안되는데?! Time.zone 을 사용하자!

위 처럼 설정파일에서 **time\_zone** 를 한국으로 설정해도 계속 시간이 UTC 로 나온다. 이유는 **Time** 클래스 그냥 사용하면 무조건 UTC 로 나오기 때문이다.

따라서 **Time** 를 사용할 때는 아래처럼 해줘야한다!

```ruby
...
start_date: Time.zone.tomorrow.midnight,
...
```

