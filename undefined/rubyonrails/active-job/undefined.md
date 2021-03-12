# 액티브잡의 기본

{% hint style="info" %}
\*\*\*\*[**RUBY ON RAILS GUIDE  - ACTIVE JOB**](https://guides.rubyonrails.org/active_job_basics.html)\*\*\*\*
{% endhint %}

## 액티브 잡은 도대체 뭐야?

액티브 잡은 작업을 선언하고 다양한 환경에서 실행되도록 하는 프레임워크다.

단순하게 백그라운드에서 예약된 스케줄링에 맞게 실행되는 프로그래램이라고 생각하면 된다.

JOB 이 예약되면 QUEUE 에 쌓이고 순차적으로 실행되는 모습이라고 생각하면 된다.

## 그러면 JOB 은 뭐야?

흠.. JOB 에 대한 정의는 보이지는 않는다. **다만 쉽게 풀어쓰면 JOB 이란 백엔드에서 언젠가 해야할 작업이고 그 언젠가가 정의된 상태인 것이라고 말할 수 있을거 같다.**

우리 레일즈를 보면 **rails s** 를 하는 순간 서버는 실행되고 사용자의 입력은 더 이상 받을 수 없는 상태가 된다. 이런 상태에서 우리가 직접 JOB 을 넣어줄수는 없다. 따라서 서버를 키는 순간 이런 JOB 을 예약시켜 놓고 시간이 되면 예약된 JOB 을 처리해 주는게 ACTIVE JOB 인듯.

## 액티브 잡의 목적

포인트는 모든 Rails 앱이 동일한 작업 인프라를 갖추도록 하는 것이다.

이 말이 무엇이냐? 실제로 백엔드에서 JOB 을 돌리는 것은 레일즈 자체에서 진행하는게 아닌 다른 API 또는 GEM 을 이용해서 한다. 즉 코드는 RAILS 로 작성하지만 이를 실행하는 백엔드 프로그램은 레일즈가 아닌 다른 무언가란것.

**Active JOB** 은 **다양한 백엔드 프레임 워크롤 이용해서** 동일한 코드로 동일한 동작을 구현할 수 있게 해준다.

**이러한 특징덕분에 백엔드 프로그램을 중간에 바꾸더라도 코드 자체는 수정되지 않아도 된다.**

레일즈에서 JOB 을 백엔드로 돌릴 수 있는 프로그램으로는

* [Sidekiq](https://github.com/mperham/sidekiq/wiki/Active-Job)
* [Resque](https://github.com/resque/resque/wiki/ActiveJob)
* [Sneakers](https://github.com/jondot/sneakers/wiki/How-To:-Rails-Background-Jobs-with-ActiveJob)
* [Sucker Punch](https://github.com/brandonhilkert/sucker_punch#active-job)
* [Queue Classic](https://github.com/QueueClassic/queue_classic#active-job)
* [Delayed Job](https://github.com/collectiveidea/delayed_job#active-job)
* [Que](https://github.com/que-rb/que#additional-rails-specific-setup)

등이 있다.

## JOB 의 실행

이전에 ACTIVE JOB 이란 동일한 코드 다양한 백엔드 프로그램에서 동일한 결과를 얻게 해주는 프레임워크라고 이야기를 했었다.

자 그렇다면 왜 ACTIVE JOB 은 자기가 직접 백엔드를 관리하지 않고 3rd party에게 JOB 의 실행을 떠넘기는 걸까?

그 이유는 rails 서버는 중간에 실행이 멈출수도 있기 때문이다. 서버가 멈춘다는 것은 그동안 쌓아왔던 모든 예약된 JOB 이 공중분해됨을 의미한다.

이런 문제 때문에 레일즈에서는 직접 JOB 을 관리하는게 아니라 3rd party 에게 JOB 의 스케줄링을 맡기는 것이다.

그래도 작은 단위의 서비스에서는 본인이 직접 백엔드 역할까지 할 수 있음\(권장되지 않음\)

## 사용법

사용법은 너무나 간단하다. 먼저 JOB 파일을 만든다.

```text
bin/rails generate job cleanup
```

```ruby
# cleanup_job.rb

class CleanupJob < ApplicationJob
  queue_as :default

  def perform(*guests)
    # Do something later
    p '------------------'
    p guests
    p '------------------'
  end
end
```

이 perform 에 다양한 입력을 줘서 실행하는 메서드를 다르게 하면 끝.

```ruby
# model.rb
...
...
CleanupJob.perform_later("haha", "hoho") # haha hooho 출력
CleanupJob.set(wait: 10.seconds).perform_later("10seconds later") # 10초뒤에 출력
```

