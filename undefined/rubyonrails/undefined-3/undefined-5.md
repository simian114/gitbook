# 문자열\(정규표현식\)

{% hint style="info" %}
\*\*\*\*[**RUBY REGEX**](https://www.rubyguides.com/2015/06/ruby-regex/)\*\*\*\*

\*\*\*\*[**RUBY STRING MATCH**](https://ruby-doc.org/core-2.5.1/Regexp.html)\*\*\*\*

\*\*\*\*[**RUBY STRING REGEX TEST!!!**](https://regexr.com/3bfsi)\*\*\*\*

\*\*\*\*[**RUBY STRING METHOD**](https://www.shortcutfoo.com/app/dojos/ruby-strings/cheatsheet)\*\*\*\*
{% endhint %}

## 상황

### 문자열 정렬하기

왜인지는 모르지만.... 루비에서는 문자열 정렬을 `string` 의 메서드로 제공하지 않고 있었다... 그렇다고 내가 직접 구현하기는 귀찮고..

### 문자열 검사하기

프론트로 부터 받은 데이터인 **anagram** 은 아래 두 가지 조건을 갖는다.

* 첫 글자는 반드시 `@` 여야한다.
* 이후의 글자는 **소문자 또는 대문자**여야한다.

위의 조건을 만족시키는 코드를 짤 때 정규표현식을 사용하지 않으면 아래와 같은 더러운 코드가 나온다...

```ruby
return errors.add(:anagram) unless anagram.first == "@"
# is_upper 이란 함수가 구현해야함..
return errors.add(:anagram) unless anagram.is_upper?
```

더 좋은 방법은 없을까? --- 있다!

## 해결

### 문자열 정렬하기

꼼수를 사용하면 된다. 꼼수란... 문자열을 배열로 만들고 정렬한 다음 다시 문자열로 만드는 것!

```ruby
sorted_str = str.chars.sort.join
```

### 정규표현식을 이용한 문자열 검사

* 첫 글자는 반드시 `@` 여야한다.
* 이후의 글자는 **소문자 또는 대문자**여야한다.

위의 조건으로 문자열을 검사하기 위해서는 아래 처럼 한줄을 써주면 된다. ㅎㅎ

```ruby
errors.add(:anagram) unless anagram.match(/^@[A-Za-z]/)
```

