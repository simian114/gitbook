# 실전! 액티브 잡을 이용한 스케쥴링

## 상황

트렌센던스 프로젝트는 **특정 시간에 이벤트가 발동해야 한다.** 예를 들면 **토너먼트** 는 몇일 동안 특정 시간에 게임이 진행되어야하고 **War** 또한 특정 시간에 이벤트가 발동해야한다.

지금 우리가 맡고 있는 War 를 예로 들어보면 아래의 경우를 스케쥴링에 등록해야한다.

### 스케쥴링 목록

1. war-request 를 상대방이 수락하는 경우
   * war의 시작과 끝. Start-date 와 end-date 에 실제로 war 가 시작되고, 종료되게 지정해야한다.
2. war 가 진행중일 때 
   * 특정 시간마다 war-time 이벤트가 발동해야한다.
3. war 가 미응답횟수 초과로 종료되는 경우
   * end-date 가 이전에 이벤트를 발동해서 war 를 종료해야한다.
4. war-battle 을 신청했는데 일정 시간동안 상대 길드에서 응답을 하지 않는다.
   * 게임 대기하고 있는 유저를 게임에서 나오게 만들어준다.
   * 모든 유저한테 게임 대기 하고 있는 유저가 없음을 알려준다.

## 해결

### 실제 구현 파일을 보자.

```ruby
# war_job.rb
class WarJob < ApplicationJob
  queue_as :default

  def perform(war, options)
    @war = war
    @options = options
    type = options[:type]

    case type
    when "war_start" then @war.start
    when "war_end" then @war.end
    when "war_time_start" then war_time_start
    when "war_time_end" then war_time_end
    when "match_no_reply" then match_no_reply
    end
  end

  def war_time_start
    return unless @war.status == "progress"
    @war.broadcast({ type: "war_time_start", current_hour: Time.zone.now.hour })
  end

  def war_time_end
    return unless @war.status == "progress"
    @war.broadcast({ type: "war_time_end", current_hour: Time.zone.now.hour })
  end

  def match_no_reply
    match = @options[:match]
    return unless match.pending?
    waiting_user = match.scorecards.first.user
    no_reply_guild_war_status = @war.war_statuses.where.not(guild_id: waiting_user.in_guild.id).first
    no_reply_guild_war_status.no_reply_count += 1
    no_reply_guild_war_status.save
    @war.end if no_reply_guild_war_status.no_reply_count > @war.request.max_no_reply_count
    match.cancel
    @war.broadcast({ type: "no_reply", user_id: waiting_user.id })
  end
end
```

코드가 길어 보이지만... 실제적으로 너무 간단한 로직을 돌리고 있음을 알 수 있다.

어떤 경로를 통해 JOB 에 등록이 된다면, perform 함수가 실행이 된다. 그리고 이 함수는 들어오는 인자에 따라 분기가 나눠져서 각 함수를 실행한다.

### perforom

기본적으로 perform 을 실행하면 지금 당장 해야하는 작업들이 있는 경우에는 그 작업들으 **queue** 로 들어가서 해당 작업들이 종료된 다음에 실행된다. 이 **perform** 에는 뒤에 접미사인 **\_now** 와 **\_later** 를 붙여 줄 수 있는데 이것들으 의미는 queue 의 가장 앞에 넣을거냐 뒤에 넣을 거냐의 차이다.

결국 **perform** 은 **현재의 큐에 JOB을 등록하고 바로 실행하는 함수다.** **따라서 우리가 원하는 스케쥴링을 실제로 담당할 수는 없다!! 왜냐하면 우리는 몇 시간, 몇 일후에 JOB 이 실행되길 원하기 때문이다.**

### set

> 이 함수와 perform 을 이용해서 우리가 원하는. 진정한 의미의 스케쥴링이 가능하게 된다.

함수의 이름은 set 이다. JOB 에서의 set 이라... 이름만으로도 어떤 동작을 할지 예상이 간다. _**그렇다 이 함수를 이용하면 우리가 원하는 예약을 할 수 있다!!**_

실질적으로 **set** 함수는 **enqueue** 하는 작업을 수행한다. 그리고 **enqueue** 된 JOB 들은 제때가 되면 알아서 실행이 되는 것.

백문이 불여일견이니 실제 사용하는 코드를 보자.

```ruby
# war.rb
def job_reservation(until_time, options)
  return if until_time.past?
  WarJob.set(wait_until: until_time).perform_later(self, options)
  puts "* Job reservation: #{options[:type]}(id: #{self.id}) at #{until_time}"
end
```

**set** 으로는 추상화가 덜 되어있으므로 추상화를 시켜서 **job\_reservation** 을 만든 모습을 확인할 수 있다. 그리고 이 함수는 내부에서 set 을 실행한다.

주목할건 인자로 들어오는 **wait\_until** 이다. 이 인자는 키워드 인자로 받고있고 이름만 들어도 어떤 역할을 하는지 추측가능하다.

**wait\_until** 은 해당 시간 도달하면 예약된 JOB 을 실행한다!!!! 물론 **perform\_later, perform\_now** 등은 꼭 붙여줘야한다.

![&#xC2E4;&#xD589; &#xACB0;&#xACFC;!! &#xC11C;&#xBC84;&#xB97C; &#xC2E4;&#xD589;&#xC2DC;&#xD0A4;&#xBA74; &#xC2E4;&#xD589;&#xB418;&#xB294;&#xAC78; &#xD655;&#xC778;&#xD560; &#xC218; &#xC788;&#xB2E4;.](../../../.gitbook/assets/.png%20%281%29.png)

