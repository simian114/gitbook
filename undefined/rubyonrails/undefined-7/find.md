# 액티브레코드 find의 다양한 활용

{% hint style="info" %}
**\`\`**[**`find_or_create_by`**](https://apidock.com/rails/v4.0.2/ActiveRecord/Relation/find_or_create_by)**\`\`**

**\`\`**[**`find_by with multiple params`**](https://stackoverflow.com/questions/10189556/ruby-rails-how-to-find-by-multiple-options)**\`\`**
{% endhint %}

## find\_or\_create\_by

> 먼저 찾고 없으면 create한다. 있으면 첫 번째 찾은 레코드를 return

DB에서 먼저 찾은 다음 만약 없으면 create 하는 함수.

exists 로 존재여부를 확인한다음 할 수도있지만 이렇게 하면 단번에 찾고, 없으면 생성한다.

실제 코드에서는 아래처럼 `user` 모델을 인자로 넘겨서 만약 찾지 못한다면 `create` 하게 만들었다.

```ruby
Friendship.find_or_create_by(user_id: params[:user_id], friend_id: params[:friend_id])
```

## find\_by with multiple params

특정 레코드를 찾을 때 하나 이상의 조건으로 검색하고 싶을 때 사용하면 된다.

실제 코드를 먼저 보자

```ruby
Friendship.find_by_user_id_and_friend_id(params[:user_id], params[:id])
```

`friendship` 모델에는 `user_id`와 `friend_id` 가 컬럼으로 존재한다. 따라서 레일즈에서는 위의 코드처럼 `find_by_` 를 찰떡같이 만들어준다. 우리는 그냥 사용하면 됨.

