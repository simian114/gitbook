# view helper로 디버깅 하는 방법

## 1. debug 메서드 사용

```ruby
<%= debug @book %>
```

## 2. inspect 사

```ruby
<%= @book.inspect %>
```

위 두 메서드는 `to_s` 와는 다르다. `to_s` 가 `for end_user` 라면 위 두개는 `for developer`

