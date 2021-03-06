# Chapter8. 메서드 파헤치기

## 8.1 메서드 정의

* `def` 키워드롤 메서드를 정의 한다. 그리고 메서드 이름은 반드시 소문자나 밑줄로 시작해야 하며, 문자, 숫자, 밑줄\(\_\)로 구성된다.
* 메서드 이름 마지막에는 `?`, `!`, `=`이 올 수 있다.
  1. `!`: 일반적으로 `!`를 갖고 있는 메서드는 이 메서드 호출의 결과로 호출 된 객체를 수정할 것을 의미함. 뱅\(bang\) 메서드라고도 불린다.
  2. `=`: 대입문의 좌변에 올 수 있는 메서드에는 마지막 이름에 등호를 붙인다. 속성\(setter\) 메서드
  3. `?`: `True`나 `False`를 반환하는 메서드에 붙인다.
* 루비의 메서드에는 기본 인자값을 지정해 줄 수 있다.

```ruby
def cool(arg1="1", arg2="2")
  ...
end
```

### 가변인자 리스트

* 개수가 정해지지 않은 가변 매개 변수를 전달하고 싶거나 하나의 매개 변수로 여러 개의 매개 변수를 모두 처리하고 싶을 때 어떻게 해야할까?

  * `일반적인` 매개변수를 ㅁ두 적어주고 맨 마지막에 오는 가변 매개 변수의 이름 앞에 별표\(`*`\) 를 붙이면 된다.
  * 이를 변수를 가변 길이로 지정한다 라고 표현함.
  * 아래의 첫 번째 예시에서 `rest`에는 `[2, 3]`이 배열 형태로 담긴다.
  * 두 번째 예시에는 2개 이상의 인자가 들어올 수 있다.
    * 만약 인자가 두 개 들어온다면 `rest`는 빈 배열이 된다.
    * 즉, `일반적인` 매개 변수에 값이 최소한 한개씩은 들어간 다음에 나머지가 `rest` 에 담긴다.

  ```ruby
  def varargs(arg1, *rest)
    ...
  end

  varargs("1", "2", "3")

  def varargs(arg1, *rest, arg2)
    ...
  end

  varargs("1", "2", "3")
  ```

### 메서드와 블록

* 일반적으로 결합된 블록은 메서드 안에서 `yield`를 사용해 호출할 수 있다.
* 하지만 마지막 배개 변수 앞에 `&` 앰퍼샌드 기호룰 붙여준다면 블릭이 `Proc` 객체로 변환되어 이 객체를 마지막 매개 변수에 대입한다. 이를 통해 블록을 나주에 사용할 수도 있다.

```ruby
class TaxCalculator
  def initialze(name, &block)
    @name, @block = name, block
  end
  def get_tax(amount)
    "#{@name} on #{amount} = #{ block.call(amount) }"
  end
end

# 뒤에 나오는 블록이 하나의 객체가 되어서 인자로 들어간다.
tc = TaxCalculator.new("sales tax") {|amt| amt * 0.075 }

tc.get_tax(100) # 여기서 인자로 들어가는 100은 블록의 인자가 된다
```

## 8.2 메서드 호출

* 메서드를 호출할 때는 수신자의 이름을 쓰고 메서드의 이름은 쓴다. 이후에 매개변수와 블록을 써준다.
  * 아래의 예시는 모든 내용이 들어가 있다.
  * `수신자`: `connection`
  * `메서드`: `download_mp3`
  * `매개 변수`: `arg`
  * `블록`: `{ |p| show_progress}`

```ruby
connection.download_mp3("arg")  { |p| show_progress(p) }
```

* 그런데 생각해보면 `puts` 와 같은 메서드는 수신자를 생략한다.
  * 수신자를 생락하면 수신자는 기본값인 현재 객체를 나타내는 `self` 가 된다.

### 메써드에 매개 변수 넘겨주기

* 모호한 표현이 아니라면 메서드 호출 시에 매개 변수 리스트를 둘러싼 괄호를 생략해도 된다.

### 메써드는 값을 반환한다

* \(반드시 반환값을 사용해야 한다는 규칙은 없지만\) 호출된 모든 메서드는 값을 반환한다.
* 메서드의 반환값은 메서드 실행 중 마지막으로 실행된 표현식의 결괏값이다.
* return 문을 쓸 필요가 없다면 생략하는 것이 루비의 표현식이다.

### 메서드 호출 시에 컬렉션 객체 확장하기

* `*`를 사용해서 아래처럼 이용할수도 있다. 파이썬의 팩, 언팩 기능

```ruby
def five(a,b,c,d,e)
  ...
end

five(1,2,3,4,5)
five(1,2,3,*[4,5])
five(*(10..14))
```

### 블록을 동적으로 사용하기

* 이 부분에 대해서는 앞에서 이미 수차례 이야기 했다.
* 블록 자체 `Proc` 객체로 만들어서 함수의 인자로 넘기는 것!

