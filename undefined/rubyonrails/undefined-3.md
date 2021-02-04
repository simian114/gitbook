# 레일즈에서 모델 관련 이슈



## 문제1. 원하는 조건, 특정 속성 만을 갖고 오는 법

### 상황

`/api/users` 에 `get` 요청을 날리면 `User` 의 모든 모델들이 날라온다.

* 하지만 우리는 **특정** **조건**을 갖는 모델의 **특정 속성** 만을 갖고 오고 싶다. 그러면 어떻게 해야하는가?

### 해결 

* **특정 조건** 은 `where` 
* **특정 속성** 은 `select`

조건과 속성 모두를 쿼리로 줄 수 있게 만들었다.

* 조건은 `key: value` 로 명시하게 함. 만약 `/api/users?status=online`라면 모델 중에서 `status = online` 인 모델만 준다.
* 속성은 `for` 쿼리로 명시하게 함. 만약 `/api/users?for=appearance` 라면 모든 모델을 가져다주면서 `id, name` \(특정 속성\)만을 가져다 준다.
* 위 둘을 합쳐서 `/api/users?status=online&for=appearance` 를 날리면
  * 모든 모델에서 `online` 인 모델을 먼저 찾고
  * 찾은 모델에서 `appearance` 조건에 맞는 속성만을 `select` 한다.

#### Example \(/api/users\)

모든 `users` 에서 **1. 현재 온라인\(playing 또는 online\)**인 `user` 의 **2. `id, name`** 만을 갖고 오고 싶을 때

```ruby
# users_controller.rb
def index # /api/users?status[]=online&status[]=playing&for=appearance
    # 1. where로 특정 조건 선택. 반복문을 돌면서 쿼리로 들어온 조건을 탐색한다. 여기서 for은 제외. 그리고 params 는 strong parameter의 permit 으로 1차적으로 걸렀다고 가정한다.
  users = User.all
  params.each do |k, v|
    next if k == 'for'
    users = users.where(k => v)
  end

  # 2. select로 선택하기
  users.select('id, name') if params[:for] == 'appearance'
end
```

* 위의 코드는 `controller`에 정의를 했다. 하지만 주요 로직은 컨트롤러가 아닌 모델에 정의되어야 한다. 따라서 모델로 옮기고 함수를 정리하자

```ruby
# user.rb

def self.where_and_select_by_query(params)
  users = where_by_query(params)
  if !params[:for].nil?
        users = select_by_query(users, params[:for])          
end

private  
def where_by_query(params)
    users = all
  params.each do |k, v|
    next if k == 'for'
    users = users.where(k => v)
  end
  users
end

def select_by_query(users, condition)
    if condition == 'appearance'
    users.select('id, name')
  else if condition == '...'
    users.select('....')
  end
end
```

## 문제2 Association 모델의 정보를 갖고 오고 싶을 때

### 상황

* `/api/users` 에 요청했을 때 길드의 정보인 `anagram` 을 갖고 오고 싶다.
* 현재 `user` 는 `guildMemberShip`을 통해 길드와 `assoc` 되어 있다.

### 해결

```ruby
# users_controller.rb
def index
  users = User.all
  params.each do |k, v|
    next if k == 'for'
    users = users.where(k => v)
  end

  # 여기가 바뀐 부분이다!
  users.left_outer_join(guild_membership: :guild).
    select('users.id, users.name, users.status, guilds.anagram')
end
```

* 리팩토은 신경끄고, `left_outer_join` 만 보면 된다.
* `guild_membership`, `guild` 모두 모델이고 `User` 는 `Guild`와 직접 연관 된게 아닌 `guild_membership` 을 통해서 연결되어 있으므로 위와 같이 `guild_membership: :guild` 로 참조해서 가져온다.

