---
description: 참 신기해
---

# Chapter4. 컨테이너, 블록, 반복자

## 4.0

* 실제로 사용되는 대부분의 프로그램은 데이터 컬렉션을 처리한다.
  * 컬렉션이란 무엇인가?
    * `C++`의 컨테이너라고 생각하자.
    * 배열과 같이 큰 집합이 있고 집합 내부에 다수의 원소를 가지고 있는 형태를 `컬렉션` 이라고함

## 4.1 배열

* `Array` 클래스는 _**객체 참조**_ 를 컬력션으로 저장한다.
* `stack`과 `pop`은 배열의 뒤에 데이터를 추가, 삭제
* `unshift`, `shift`는 배열의 앞에 데이터를 추가, 삭제

## 4.2 해쉬

* 객체 참조가 색인된 컬렉션이다.
* 해시는 `key`, `value`가 쌍이라는 특징으로 인해 하나의 원소를 추가하기 위해서는 두 개의 객체가 필요하다.\(왜냐하면 루비는 모든게 객체이므로 키 또한 객체\)
* 일반적으로 `=>`를 사용하지만, 심벌을 키로 사용할 경우에는 축약해서 `:`을 사용할 수 있다.
  * 일바적으로 키는 심볼을 사용하는 경우가 많다.
* 루비는 해시에 요소를 추가한 순서를 기억한다. 따라서 `each`등의 메써드로 순차적으로 원소를 빼올 때 추가한 순서 그대로가 반환된다.
  * 색인 되었다는 것은 보통 키를 값으로 해서 이진 트리를 만들었다는 것이다. 따라서 일반적으로는 색인되었기 때문에 순서를 기억하지 않을 것이라고 생각할 수도 있지만 루비는 순서를 기억함. 매우 중요한 특징?
* 초기값을 지정할 수 있다. 아래처럼 `new` 메써드의 인자로 초기값을 주자.

  ```ruby
  test = Hash.new(0)
  ```

## 4.3 블록과 반복자.

```ruby
for i in 0..4
  word = top_five[i][0]
  counts = top_five[i][1]
  puts "#{word}: #{count}"
end
```

* 위의 코드는 아무런 문제없이 동작한다. 하지만 반복문을 보면 5번이 실행된다는 것을 알 수 있는데 이는 원소의 인자가 정말 5개일 때만 사용할 수 있는 코드다. 만약 5개보다 적다면 반드시 에러가 날 상황.
* 루비의 반복문은 `C`, `C++` 에 비해서 더 추상화된 형태다. 따라서 반복을 조금 더 고상하게, 사람이 이해할 수 있게 나타낼 수 있다.
* 위의 코드를 루비스럽게 표현한 것. `each` 메써드를 통해 컬렉션에서 원소들을 하나씩 빼온다. 그리고 나오는 인자 2개를 출력한다.
  * 이 경우는 `top_five`의 클래스에 `each` 메서드가 따로 구현되어 있고 내부적으로는 `yield` 될 때`word`와 `count` 를 인자로 해서 내보낸다.

    ```ruby
    top_five.each do |word, count|
    puts "#{word}: #{count}"
    end
    ```

### 블록

* 블록은 중괄호나 `do end` 키워드로 둘러싸인 코드 덩어리다. 이 두개는 연산자 우선순위를 제외하면 완전히 동일하다.
* 보통 한줄인 경우 중괄호를 사용하고 여러 줄일 경우 `do end` 키워드를 사용한다.
* 블록은 `익명 메써드`의 일종이다.
* 블록은 반드시 메서드 호출 바로 다음에 위치해야한다.

  ```ruby
  sum = 0
  [1,2,3,4].each do |value|
  square = value * value
  sum += square
  end
  puts sum
  ```

* 위와 같은 코드가 있을 때 블록은 각 배열 요소에 대해 한 번씩 호출된다.
* 주의할 것은 변수 `sum` 이다. 이 변수는 블록이 호출되기 전에 더 큰 범위에서 선언된 변수다. 그리고 블록 내부에서도 사용되는 것을 볼 수 있다.
  * 여기서의 `sum`은 블록 밖의 변수지만 블록 내부에서도 그 영향일 미치고, 블록이 끝난 이후에도 여기서 바뀐 값이 적용된 상태다. 즉 마지막 `puts sum` 의 값이 0 이 아니라 블록의 결과라는 것.
* 만약 블록 밖에서 이미 선언된 값이라도 무시하고 블록 내부에서 사용하고 싶을 때는 아래처럼 `;` 를 붙인다.

  ```ruby
  sum = 0
  [1,2,3,4].each do |; value|
  square = value * value
  sum += square
  end
  puts sum
  ```

### 반복자 구현하기

* 루비에서 반복자란 코드 블록을 호출할 수 있는 메써드를 의미한다.
* 블록과 메써드는 동시성? 있게 실행이 된다.
* 메써드에서는 여러 번 `yield`를 할 수 있다. 이 말은 즉, 하나의 메써드는 여러 번 블록을 호출 할 수 있다는 것이다. 그리고 각각 `yield`를 할 때마다 메써드 자신의 상태는 저장되는 상태고 다음에 `yield`를 할 때는 이전과는 다른 모습을 하고 있다. 정리해보자면 `yield`를 하나의 함수라고 봐도 무방하다는 것이다.
* 블록이 재밌는 점은 매개 변수를 블록에 넘겨줄 수도 있고 블록의 실행결과를 다시 받아올 수도 있다는 점이다.
* 블록은 메서드에 자신의 평가 결과를 반환한다. 아래 예시는 배열의 `find`를 간단하게 구현해 본 것이다. `each` 를 통해서 각 원소를 `yield` 하는데 만약 `yield`의 결과 값이 `참`이라면 그 원소를 `return`한다.
  * 블록이 메서드에 자신의 평가 결과를 반환한다는 것은 맨 아래에서 확인할 수 있다. `v * v > 10` 을 보자. `yield` 의 인자의 제곱이 10 보다 크면 `true`라는 값으로 평가한다는 것.

```ruby
class Array
  def find
    each do |value|
      return value if yield(value)
    end
    nil
  end
end

[1,2,3,4,5].find { |v| v * v > 10 }
```

#### each

* each 는 루비에서 특별한 반복자다.
* 일반적으로 `each` 는 컬렉션에서 원소의 값을 순차적으로 `yield` 해준다.

#### collect 또는 map \(collect와 map은 같음\)

* `each` 와 거의 동일하지만 `collect`와 `map`은 평가 결과를 컬렉션으로 만든다.
* 즉 블록 실행 결과 하나의 컬렉션이 만들어진다고 할 수 있다.

```ruby
arr1 = [1, 2, 3, 4, 5]
arr2 = arr1.map { |v| v * 2}
```

* 위 코드를 분서하면 `arr1.map`을 통해서 배열의 원소가 순차적으로 `yield` 된다. 그리고 그 값의 `* 2`가 새로운 컬렉션 - 배열에 쌓이게 되고 최종적으로 만들어진 배열을 `arr2`가 참조하게 된다.

#### with\_index

* 반복자들이 컬렉션에서 원소들을 꺼내어주는건 좋다. 하지만 인덱스가 없잖아?!
* with\_index를 사용하면 인덱스와 같이 꺼내진다.
  * `yield` 의 인자가 원소 하나가 아닌 `index` 도 같이 있다고 생각하면 쉽다.

    ```ruby
    arr1 = [1, 2, 3, 4, 5]
    arr1.each.with_index do |v, index|
    puts "value: #{v}, index: #{index}"
    end
    ```

    **inject**
* 이 메서드는 특정한 연산을 `누적`해서 적용한다.

### 열거자\(Enumerator\)와 외부 반복자

* 루비에서는 반복자가 컬렉션의 내부에 있다.
  * 즉 반복자는 다른 메써드와 다를바 없는 단순한 메써드다.
  * 반복자는 새로운 값을 위해 매번 `yield`를 호출한다.
  * 반복자를 이용하는 주체는 바로 메서드에 결합된 블록이다.
  * 보통 다른 언어에서는 컬렉션이 자신의 반복자를 포함하지 않는다.
    * `C++`를 생각해보면 반복자를 위해 `iterator`라는 클래스가 존재하고 각 컨테이너들은 이 `iterator` 사용하는 거지 자신의 메써드 자체로서 가지고 있지는 않다.
* 루비의 컬렉션들은 자신이 직접 반복자를 가지고 있다.
  * `C++`를 생각해보면 우리는 반복자 만을 따로 떼서 코딩했던 경험이 분명있다. 즉 반복자 그 자체로서가 의미가 있었던 것.
  * 그런데 루비는 컬렉션 자체에 반복자가 있으므로 `반복자 하나만을 떼서 사용할수는 없다?`
    * 이건 아니다.
  * 즉 루비에서도 반복자만을 따로 빼서 - 정의해서 사용할 수 있다.
* 열거자\(`Enumerator` 클래스\)
  * 배열이나 해시에 대해 `to_enum(== enum_for)` 메서드를 호출하는 것만으로 `Enumerator` 객체를 생성할 수 있다.
  * 아래는 peek과 next의  사용 예시

    ```ruby
    a = [1,2,3]
    e = a.to_enum
    p e.next   #=> 1
    p e.peek   #=> 2
    p e.peek   #=> 2
    p e.peek   #=> 2
    p e.next   #=> 2
    p e.next   #=> 3
    p e.peek   #raises StopIteration
    ```

  * 대부분의 내부 반복자와 메서드들은 블록 없이 호출하면 `Enumerator` 객체를 호출한다. 즉 `enum_c = a.each` 라는 문장이 있으면 `enum_c`는 `Enumerator` 라는 것.
  * `loop`
    * 이 메서드는 블록을 그저 반복적으로 실행하기만 한다.
    * 사용시에는 특정한 조건에 의해 반복이 정지되도록 하는게 좋음.
    * `Enumerator`와 같이 사용하면 깔끔 편리하다.
  * `each_with_index` 메서드 \(`Enumerator` 또한 클래스이므로 메써드를 갖는다.\)
    * `each` 메서드를 `index` 와 같이 내보낸다.

### 제네레이터\(Generator\)로서의 열거자, 필터\(filter\)로서의 열거자

* 이미 존재하는 컬렉션으로부터 열거자를 생성할 수도 있지만, _**명시적으로 블록을 넘겨 열거자를 생성할 수도 있다.**_
* 블록의 코드는 열거자 객체가 프로그램에 새로운 값을 생성해서 전달할 때 필요하다. 하지만 이 블록이 단순히 위에서 아래로 실행되지는 않는다.
  * 블록은 선형적으로 실행되느 ㄴ대신 프로그램의 나머지 부분과 병렬로 실행된다.
  * 블록은 위에서부터 시작하지만 블록이 특정한 값을 `yield` 하는 시점에서 실행을 멈춘다.
  * 프로그램에서 새로운 값을 필요로 할 때 다시 이를 호출하면 이전에 블록에서 멈춘 부분부터 다시 다음 값을 `yield` 할 때 까지 프로그램을 호출한다.
  * 위와 같은 논리를 이용해서 무한히 값을 생성할 수 있는 열거자를 생성할 수 있다. \(물론 더욱 다양한 일을 할 수 있음\)
* `triangular_numbers`는 `Enumerator::Generator`다.
  * 아래의 예시에서 `triangular_numbers` 로 만든 열거자는 무한 반복할 수 있다.
  * `first` 메써드는 열거자에서 처음 몇 개 까지의 인자를 가져오는 역할을 한다.

```ruby
triangular_numbers = Enumerator.new do |yielder|
  number = 0
  count = 1
  loop do
    number += count
    count += 1
    yielder.yield number
  end
end

# first 메써드는 열거자에서 인자만큼의 개수를 앞에서 가져옴
p triangular_numbers.first(5) # => [1, 3, 6, 10, 15]
```

* 위의 예시에서 조심해야 할 것이 있다. `first`의 메써드는 정말 정해진 개수만큼을 가져오지만 `count` 또는 `select`는 조건에 부합하지 않으면 무한히 실행되는 열거자에서 빠져나오지 못하는 문제에 빠질 수도 있는 것.
  * 무한히 반복되는 열거자에서 `select`는 보통 직접 작성해야할 필요가 있다.
  * 이런 `select`, `count`를 _**탐욕적이지 않다**_ 라고 표현한다.

### 루비2의 게으른 열거자

* 열거자를 통해 무한한 시퀀스를 생성하는데에 문제가 있음을 확인했다.
* 루비 2.0에는 `select` 와 같이 탐욕적이지 않은 특별한 메써드들을 직접 구현할 필요 없다. 내장되어 있기 때문.
* 어떤 루비 열거자든 `Enumerator#lazy` 메서드를 호출하면 `Enumerator::Lazy` 객체를 반환한다. 이 열거자는 원래의 열거자와 같은 방식으로 작동하지만 무한한 시퀀스에 대해서도 정상으로 작동하도록 `select` 난 `map` 과 같은 메써드들이 재정의 되어 있다.
  * 즉 게으른 버전의 메서드들은 데이터가 요청되기 전까지는 어떠한 데이터도 사용하지 않는다. 그리고 데이터가 요청되었을 때만 필요한 만큼 사용한다.

```ruby
def Integer.all
  Enumerator.new do |yielder, n: 0|
    loop { yielder.yield(n += 1)}
  end.lazy
end

p Integer.all.first(10) # => [0,1,2,3,4,5,6,7,8,9]

p Integer
      .all
      .select {|i| (i % 3).zero? } # => 무한한 시퀀스를 만드는 문장. 하지만 lazy 객체기 때문에 Generator만 출력된다. 이런 문장을 출력하고 싶다면 여기서는
p Integer
      .all
      .select {|i| (i % 3).zero? }
      .first(10) # 이렇게 갯수를 지정해줘야함
```

* `select` 블록문을 `proc`으로 만들면 더 쉽게 표현할 수 있음.

  ```ruby
  multiple_of_three = -> n { (n % 3).zero? }
  p Integer
      .all
      .select {&multiple_of_three}
      .first(10)
  ```

### 객체로서의 블록

* 블록은 익명 메써드와 비슷하지만 단순히 이걸로 끝은 아니다.
  * 블록은 객체로 변환할 수 있으며 이 객체를 변수에 저장할 수도 있고, 어딘가에 넘겨줄 수도 있으며 또한 나중에 호출할 수도 있다.
* 블록은 메서드에 넘겨지는 추가적인 매게 변수로 생각해도 무방하다. 시실 암묵적인 매개 변수일 뿐 아니라 명시적인 매개변수로 사용할 수도 있다.
* 메서드를 정의할 때 마지막 매개 변수에 `& 앰퍼샌드` 기호를 접두사로 뭍이면 루비는 이 메서드가 호출될 때 마다 코드 블록이 넘겨졌는지 찾아본다.
  * 이 코드 블록은 `Proc` 클래스의 객체로 변환되어 매게 변수로 넘겨진다.
* 아래의 예시는 특정 인스턴스 메서드에서 `Proc` 객체를 생성하고 이를 인스턴스 변수에 저장한 다음 다른 인스턴스 메서드에서 이를 호출하는 예제다.

```ruby
class ProcExample
  def pass_in_block(&action)
    @stored_proc = action
  end
  def use_proc(parameter)
    @stored_proc.call(parameter)
  end
end

eg = ProcExample.new
eg.pass_in_block { |param| puts "The parameter is #{param}"}
eg.use_proc(99) # => The parameter is 99
```

* 위의 예를 분석해보면 `eg.pass_in_block`에서 블럭을 `인자`로서 넘긴다.
  * 이게 가능한 이유는 `pass_in_block` 메써드의 인자에 `&` 기호가 있기 때문. 이  기호는 블록을 인자로서 받아들인다.
  * 따라서 블록 자체가 `@stored_proc`에 저장되고 `use_proc` 메써드에서는 `call`을 통해 블록을 실행한다.
  * 블록을 실행할 때 `call`에 인자를 넣어서 사용했다. 이 인자가 블록의 인자로서 들어가게 된다.
* 루비는 이렇게 블록을 객체로 변환하는 내장 메써드를 두 가지 지원한다.
  1. `lambda`
  2. `Proc.new`
* 두 메서드는 블록을 받아서 `Proc` 객체를 반환한다. 반환된 객체는 약간 다르게 작동한다.\(책 416페이지에서 구체적으로 다룸\)

```ruby
lambda_test = lambda { |param| puts "You called me with #{param}"}
lambda_test.call 99
lambda_test.call("cat")

proc_new_test = Proc.new() { |param| puts "You called me with #{param}"}
proc_new_test.call 99
proc_new_test.call("cat")
```

### 블록은 클로저이기도 하다

* 블록 내부에서 블록의 외부 스코프에 있는 지역변수도 참고 가능하다는 것을 이야기 했었다. 이를 활용해서 특이하게 블록을 활용할 수 있다.

```ruby
def n_times(things)
  lambda { |n| thing * n }
end
# 위 함수는 lambda 객체를 return 한다!

p1 = n_times(23)
puts p1.call(3) # => 69
puts p1.call(4) # => 92
p2 = n_times("Hello ")
puts p2.call(3) # => Hello Hello Hello
```

* `n_times` 메써드는 이 메서드의 매개변수 `thing`을 참조하는 `Proc` 객체를 반환하다.
  * 이 매개 변수 블록이 호출될 때 스코프 범위 밖에 있지만 블록에서는 당연하다는 듯이 사용할 수 있다. 이를 _**클로저**_ 라고 한다.
  * 쉽게 말해서 `p1 = n_times(23)` 을 하면 `p1` 에는 `Proc` 객체가 담긴다. 그리고 이 `Proc` 객체는 `call` 메써드를 이용해서 자신의 블록을 호출할 수 있다. 여기서 클로저의 개념이 나오는데, lambda로 만들어진 `p1` 의 블록에 `thing` 는 미리 선언된 지역변수의 개념으로 사용되기 때문에 여기서 위와 같은 결과가 나올 수 있다는 것.

```ruby
def power_proc_generator
  value = 1
  lambda { value += value }
end

power_proc = power_proc_generator

puts power_proc.call # => 2
puts power_proc.call # => 4
puts power_proc.call # => 8
```

* 위 예시로 알 수 있는건 `power_proc`이 참조하는 `Proc` 객체 내부의 `value`의 값들은 계속 그 값을 유지한다는 것!

### lambda 의 대체 문법

* `Proc`로 객체를 생성하는 방법을 두 가지 배웠다. `Proc.new` 와 `lambda`. `lambda`는 아래 처럼 축약으로 작성할 수 있다.
  * `lambda { |param| ...}` == `-> param { ... }`
* 하나 이상의 `Proc` 객체를 메서드에 넘겨줄 때는 `->` 방식이 선호된다.

```ruby
def my_if(condition, then_clause, else_clause)
  if condition
    then_clause.call
  else
    else_clause.call
  end
end

5.times do |val|
  my_if val < 2,
        -> { puts "#{val} is small"}
        -> { puts "#{val} is big"}
#0 is small
#1 is small
#2 is big
#3 is big
#4 is big
```

