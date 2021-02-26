# CASE를 이용해서 정렬\(일반적인 정렬 X\)

{% hint style="info" %}
\*\*\*\*[**STACKOVERFLOW를 참고하자!**](https://stackoverflow.com/a/66329633)\*\*\*\*
{% endhint %}

## 상황

길드에는 세 개의 클래스가 존재한다. **master, officer, member**

프론트에서 백엔드에 현재 길드의 유저 정보를 요청하는데, _**그 유저들이 클래스로 정렬되어 있기를 원할 때는 어떻게 해야하는가?**_

## 해결

### CASE 를 사용하자!!

실제 사용된 코드는 아래와 같다.

```ruby
def self.priority_order
  self.order(
    # A Relational Algebra
    Arel.sql(<<-SQL.squish
      CASE
      WHEN position = 'master' THEN '1'
      WHEN position = 'officer' THEN '2'
      WHEN position = 'member' THEN '3'
      END
      SQL
      )
    )
end

# 이후에는 아래처럼 사용하면 된다.
def self.for_members_ranking(guild_id, page)
  self.where(guild_id: guild_id).priority_order.page(page).map {                 |membership|
        membership.user.profile
    }
end
```

여기서 주의할 건 `Arel.sql(<<)` 문인데, 이 부분은 레일즈 버전이 업 되면서 추가된 부분이다.

