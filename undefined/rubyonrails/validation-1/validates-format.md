# validates format\(정규표현식\)

{% hint style="info" %}
\*\*\*\*[**Anchors - \A, \z ..**](https://docs.ruby-lang.org/en/2.4.0/Regexp.html#class-Regexp-label-Anchors)\*\*\*\*

\*\*\*\*[**RIP tutorial**](https://riptutorial.com/ruby-on-rails/example/14381/validates-format-of-an-attribute)\*\*\*\*

\*\*\*\*[**validate\_format\_of**](https://apidock.com/rails/ActiveModel/Validations/ClassMethods/validates_format_of)\*\*\*\*

\*\*\*\*[**validate - 한글 정리**](https://atsequence.tistory.com/69)\*\*\*\*
{% endhint %}

## 상황

`guild_create` 을 할 때 길드의 `name` 에 대해서 허용하는 문자는 `alpha-numeric` 으로 제한하고 싶다... 기존에는 `check_anagram` 에서 아래처럼 해주고 있었다.

```ruby
return errors.add(:anagram, :invalid, message: "아나그램에 특수문자가 포함될 수 없습니다.") unless anagram.match(/^@[A-Za-z0-9]/)
```

그런데 나는 또 `check_anagram` 과 같은 함수를 만들고 싶은 생각은 없다. `validates` 로 할 수 있는 방법은 없을까?

## 해결

### validate format 을 사용하면 된다.

코드는 아래와 같다.

```ruby
validates :name, format: { with: /\A[A-Za-z0-9]+\z/, message: '길드 이름에는 특수 문자가 포함될 수 없습니다.'}
```

`validates :column, format{ with: /regex/}` 와 같은 문법을 사용한다.

여기서 주의 해야할 건, `\A` 와 `\z` 다. 이 두 가지는 `Anchors` 라고 불린다. [**여기를 참고하자.**](https://docs.ruby-lang.org/en/2.4.0/Regexp.html#class-Regexp-label-Anchors)

* `\A`: Matches beginning of string.
* `\z`: Matches end of string

