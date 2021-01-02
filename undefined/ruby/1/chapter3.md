# Chapter3. 클래스, 객체, 변수

## 3.0 클래스, 객체 변수

루비에서 다루는 모든 것은 객체다.

* 클래스의 인스턴스를 생성할 때는 `new` 메써드를 사용한다.
* 생성자 메서드를 정의하려면 `initialize` 메서드를 만들면 된다.
  * 생성자를 정의하는 일반적인 방법이다.
* `p` 메서드는 객체의 내부 상태를 출력한다.
* `puts` 메써드는 클래스의 `to_s` 메서드를 출력한다.
  * 만약 `to_s` 가 따로 정의되어 있지 않다면 기본적인 내용만 출력한다.
* `to_s`는 객체를 문자열로 나타내주는 표준이다.

  ```ruby
  class Book
  ...
  b1 = Book.new()
  p b1
  puts b1
  ```

  **3.1 객체와 속성**

* 객체의 내부가 외부에 노출되는 부분을 `객체의 속성`이라고 한다.
  * `C++` 에서의 `setter`, `getter` 라고 생각하자.
* 속성을 정의하는 방법은 아래와 같다.\(getter와 setter\)

  ```ruby
  class Book
  def initialize(isbn, price)
    @isbn = isbn
    @price = price
  end
  # Getter
  def isbn=(isbn)
    @isbn = isbn
  end
  def price=(price)
    @price = price
  end
  Setter
  def isbn
    isbn
  end
  def price
    price
  end
  end
  ```

* 루비에서는 위의 속성을 아주 간단하게 만들 수 있다. \(다만 아래처럼 만들면 예외처리 등의 무시되므로 그냥 직접하는게 좋을듯? 원하지 않는 타입으로 세팅될 가능성이 매우 높음\)
  1. 읽기 속성
     * `attr_reader :isbn, :price`
  2. 쓰기 속성
     * `atter_writer :isbn, :price`
  3. 읽기 쓰기 한번에
     * `attr_accessor :isbn, :price`

       **가상속성**

       가상속성을 어렵게 생각할 필요는 없다. 위의 `Book` 클래스에서 `price` 의 단위가 달러인데 우리는 센트로 값을 세팅하고 싶다? 그러면 아래처럼 해주자.

       ```ruby
       class Book
       ...
       def price_in_cents=(cents)
       price = 1000 * cents
       end
       ...
       end
       ```

       여기서 `price_in_cents`에 대응하는 인스턴스 변수는 존재하지 않는다. 하지만 우리는 가상으로 `price_in_cents` 라는 속성을 만들었다 이게 가상속성의 개념.

### 속성, 인스턴시 변수, 메써드

* 속성은 메서드의 특이한 형태일 뿐이다.
* 굳이 속성을 빡빡하게 정의하려고 하지말자.

## 3.3 접근 제어

* 클래스 인터페이스르 설계할 때 클래스를 외부에 어느 정도까지 노출할지 결정하는 것은 중요한 일임.
* 접근제에는 총 3가지 `public`, `protected`, `private` 등이 있다.
  * `public`: 누구나 호출할 수 있다. 아무런 접근제어가 없는 상태. `class` 에서 접근제어를 특별히 하지 않는다면 `public` 으로 만들어진다.
  * `protected`: 그 객체를 정의한 클래스와 하위 클래스에서만 호출할 수 있다.
  * `private`: 수신자를 지정해서 호출할 수 있다. 왜냐하면 수신자는 항상 `self` 이기 때문.
* 루비는 접근 제어가 동적으로 결정된다.

### protected

-`public`와 `private`는 개념적으로 간단한데 `protected` 는 아리쏭하다. 간단하게 언제 사용되는지 살펴보자.

```ruby
class Book
  ...
  def more_expensive?(other)
    @price > other.price
  end
  ...
end
```

* 위의 경우를 보면 `other`라는 변수가 있는데 이 변수는 `Book` 의 인스턴스다!
* 즉 `protected` 접근은 객체가 같은 클래스에서 생성된 다른 객체의 상태에 접근할 필요가 있을 때 사용한다.

## 3.4 변수

* 변수는 객체의 자취를 저장하기 위해 사용된다.
* 객체를 만들면 이 객체는 변수에 저장이 된다.
  * 그렇다면 `변수`는 객체인가?
    * 정답은 `아니오` 다.
    * 변수는 단순히 객체에 대한 `참조`를 가지고 있을 뿐이다.

```ruby
person1 = "Tim"
person2 = person1

person1[0] = 'J'
puts person1
puts person2
```

* 위의 결과 `person1` 과 `person2`는 같은가?
  * 정답은 같다.
  * 왜냐하면 변수 perso1 은 "Tim" 이라는 문자열 객체에 대한 참조를 가지고 있었고, person2 는 person1을 받았다. 즉 "Tim" 이라는 문자열 객체의 참조를 받은 것. 따라서 객체 자체가 바뀌면 person1이나 person2 가 가리키는 문자열 객체 모두 바뀐다.
* `freeze` 메써드

  * 이 메써드는 객체의 상태를 변경할 수 없게 만든다.

  ```ruby
  person1 = "Tim"
  person2 = person1

  person1.freeze

  person1[0] = 'J'
  ```

  * 위와 같이 동결시키고 상태를 변경하려고 시도하면 예외가 발생한다.

