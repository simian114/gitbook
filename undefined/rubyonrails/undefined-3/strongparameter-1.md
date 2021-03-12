# StrongParameter 일반데이터와 객체 데이터 한번에 받기

## 상황

프론트에서 서버로 데이터를 건내줄 때 **쿼리와 바디** 두 가지 방법으로 보내준다.

1. 쿼리: `/api/users/:user_id/chat_bans`
2. 바디

   ```javascript
   body: {
     user: {
         id: id,
         name: name,
         status: status,  
     },
   };
   ```

그리고 이 데이터를 rails 의 컨트롤러에서 받고, strong-parameter로 처리를 해야하는데 만약 body 만 온다면 아래처럼 받을 수 있다.

```ruby
def chatBansParams
    params.require("users").permit(:id, :name)
end
```

하지만 우리의 경우 쿼리로도 객체로 쌓여지지 않은 데이터가 날라오기 때문에 이 이 쿼리 또한 **strong-parameter** 로 걸러서 받아줘야 한다. 그렇다면 이 데이터는 어떻게 받을 수 있을까?

## 해결

간단하다. **permit** 만 사용하면 된다. 아래 코드처럼하면된다.

```ruby
def chatBansParams
  params.permit(:user_id, users: [:id, :name])
end
```

