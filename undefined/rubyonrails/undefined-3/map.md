# 컬렉션 map에서 요소 스킵하는법

{% hint style="info" %}
\*\*\*\*[**STACKOVERFLOW는 모든 것을 알고있다.**](https://stackoverflow.com/questions/12408561/exclude-option-from-collection-map-in-ruby-on-rails/12408717)\*\*\*\*
{% endhint %}

## 상황

특정 객체를 담아놓은 `requests` 라는 컬렉션이 있다. 컬렉션의 각 요소를 돌면서 특정 함수를 적용한 결과를 얻고 싶다면 **map** 을 사용하면 된다. 하지만 모든 요소에 적용하는게 아닌 특정 요서에만 적용하고 싶을 때는 어떻게 해야할까?

일반적인 반복문이라면 **continue** 를 사용하면 되지만 루비의 **map** 에서는 `continue` 를 사용할 수 없다.

## 해결

[역시 정답은 stackoverflow](https://stackoverflow.com/questions/12408561/exclude-option-from-collection-map-in-ruby-on-rails/12408717) 에 있었다. 결론은 _**reject**_ 메서드를 사용하는 것. 이 메서드를 사용함으로써 특정 조건에 해당하는 값을 처리할 수 있게 됐다.

실제 사용법은 아래와 같다.

```ruby
  scope :for_guild_index, -> (guild_id) do
    WarRequest.joins(:war_statuses).where(war_statuses: {guild_id: guild_id, position: "enemy"}, status: "pending")
    .order(start_date: :asc)
    .reject { |request| request.update(status: "canceled") if request.start_date.past? }
    .map { |request| 
      challenger_guild_stat = request.war_statuses.find_by_position("challenger")&.guild.profile
      war_request = request.as_json(except: [:start_date, :end_date, :war_time])
      war_request['start_date'] = request.start_date.strftime("%F")
      war_request['end_date'] = request.end_date.strftime("%F")
      war_request['war_time'] = request.war_time.strftime("%H")
      war_request.merge!( challenger: challenger_guild_stat )
    }
  end
```

5번째 줄에서 `reject` 가 사용되고 있다. 기존에는 `where` 로 걸러진 컬렉션에 대해서 모든 요소마다 `map` 을 적용했었는데, 이제는 특정 조건에 따라서 `map` 을 적용하지 않아야 한다. 따라서 `map` 으로 가기전에 **reject** 를 사용해서 거르는 작업을 한다.

이 때 필터링 작업도 하면서 동시에 특정 작업까지 진행하고 있다.

