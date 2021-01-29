# view helper로 디버깅 하는 방법

## 1. debug 메서드

```ruby
<%= debug @book %>
```

## 2. inspect 메서

```ruby
<%= @book.inspect %>
```

위 두 메서드는 `to_s` 와는 다르다. `to_s` 가 `for end_user` 라면 위 두개는 `for developer`

{% hint style="info" %}
[공식 홈페이지](https://rubykr.github.io/rails_guides/debugging_rails_applications.html)에 잘 설명 되어 있다. 
{% endhint %}

