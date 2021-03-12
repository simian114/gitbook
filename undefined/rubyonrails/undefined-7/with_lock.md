# 한 레코드에 동시 접속 막자!! with\_lock

{% hint style="info" %}
\*\*\*\*[**Race Condition And Rails with\_lock**](http://blog.katpadi.ph/race-condition-and-rails-with_lock/)\*\*\*\*

\*\*\*\*[**API**](https://api.rubyonrails.org/classes/ActiveRecord/Locking/Pessimistic.html)\*\*\*\*
{% endhint %}

## 상황

war-battle-view 에는 **상황마다 다른 버튼이 나오고 있다.** 그리고 이 **상황이란건 프론트의 정보가 백엔드로 갔을 때 서버에서 이를 처리하고 다시 프론트로 넘겨줌으로써 수정이된다.**

이렇게 상황이 변경된다면 유저는 자신의 상황에 맞는 버튼이 띄어진다.

**문제는, 어떤 유저가 상황을 바꾸기 위해 버튼을 눌렀는데 이를 백엔드에서 처리하기 전에 프론트의 다른 유저가 버튼을 누르는 상황이다.**

우리의 경우 예를 들면

1. A의 길드원이 war-battle 을 신청한다. 이 정보가 액션케이블을 통해 브로드캐스팅되서 전쟁에 참여중인 모든 유저에게 전달된다.
2. B의 길드원 중 한명은 1에서 날린 정보를 받기 전에\( 즉 상황이 변하기 전에\) 버튼을 누른다.
3. 이렇게 되면 **A, B 길드 모두 서로에게 Battle 을 요청한 상태가 된다.**

## 해결

버튼을 누르면 그 동작의 결과는 상당히 빠르게 모든 길드원에게 전달된다.

**하지만 아무리 빠르게 전달이 된다한들 백엔드에서 해결을 해주지 않으면 문제는 어떻게든 다시 발생할 수 밖에 없다.**

문제의 해결 방법을 알기 전에 코드를 먼저 보자.

```ruby
def create_dual_match_for(user, rule_id, target_score)
  match =
    Match.create(
      match_type: 'dual',
      status: 'pending',
      rule_id: rule_id,
      target_score: target_score,
      )
  card = Scorecard.create(user_id: user.id, match_id: match.id, side: 'left')
  user.update_status('playing')
  match
end
```

지금 문제가 발생했을 때 A, B 길드원 모두 match 를 만드는게 문제이므로 **동시에 접근하는 행동을 막아야한다.**

동시에 접근하는 행동을 막기 위해서는 **적절한 무언가를 찾고 그 무언가에 LOCK 을 거는 걸어야한다.** 자 그러면 적절한 무언가는 무엇인가? 아주 간단하다. 문제는 **WAR** 에서 발생하므로 **WAR** 와 관련된 무언가를 찾고 **Lock** 을 걸면 되는것!!!

```ruby
def create_war_match(user, params)
  war = War.find(params[:war_id])
  match = nil
  war.with_lock do
    raise "War match 이미 진행 중" if war.pending_or_progress_battle_exist?
    create_params = {
      match_type: params[:match_type],
      status: 'pending',
      rule_id: params[:rule_id],
      target_score: params[:target_score],
      }
    match = war.matches.create(create_params)
    card = match.scorecards.create(user_id: user.id, side: 'left')
    user.update_status('playing')
    war.set_schedule_at_no_reply_time(match)
  end
  match
end
```

A, B 두 길드는 War 를 공유하므로 war 를 찾은다음에 **Lock** 을 걸면된다~~

