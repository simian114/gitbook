---
description: 아주 재밌었다. 앞으로 자주 사용해야겠다.
---

# Chapter8. 테스트

먼저 unit테스트, Functional 테스트 등 작은 단위의 테스트에 대해 학습을 한다.

이후 실제 사용자의 행동를 모방하고, 테스트 하는 `Integration 테스트` 를 학습한다.

## 8.1 테스트

* 애플리케이션에서 테스트는 빼놓을 수 없는 작업이다.
* 코드를 작성하고 브라우저를 통해 올바른 결과가 나오는지를 확인하는 것도 좋은 방법이다. 하지만 이런 방법은 자동화가 되지 않고 개발자가 직접 하나하나 확인해야 하므로 효율상 좋지 않다.
* 그렇다면 어떤 방법이 효율성이 좋은 테스트인가? 당연 자동화 시키는 것.
* 또한 Rails는 자동화된 테스트를 굉장히 중요시한다. 아래와 같은 테스트를 지원한다.

| 테스트 이름 | 설명 |
| :--- | :--- |
| Unit 테스트 | 모델 또는 뷰 헬퍼의 동작 확인 |
| Functional 테스트 | 컨트롤러와 템플릿의 호출 결과 확인\(상태 코드 또는 템플릿 변수 뷰의 출력 결과 등\) |
| Integration 테스트 | 여러 개의 커트롤러를 넘나들며 사용자의 실제 조작을 모방하고 결과 확인 |

* 여기서는 테스트 프레임워크로 Rails의 표준인 `Test::Unit`을 살펴볼것임.

## 8.2 테스트 준비

> Rails에서 테스트를 하려면 데이터베이스와 테스트 전용 데이터가 필요하다

### 8.2.1 테스트 데이터베이스 구축

> rake 명령어를 사용

| 명령어 | 설명 |
| :--- | :--- |
| `rake db:test:clone` | RAILS\_ENV 옵션에서 지정한 데이터베이스로부터 스키마 정보\(schema.rb\)를 생성하고, 그 내용을 기반으로 테스트 데이터베이스를 구축 |
| `rake db:test:clone_structure` | 개발 환경의 데이터베이스로부터 `.sql` 파일을 생성하고, 그것을 기반으로 ㅔ스트 데이터베이스를 생성 |
| `rake test:prepare` | 미실행 마이그레이션이 없다는 거슬 확인하고 `d:test:load`를 실행 |
| `rake db:test:load` | 현재 `schema.rb`로부터 테스트 데이터베이스를 재생성 |
| `rake db:test:purge` | 테스트 데이터베이스를 제거 |

* 터미널에 다음과 같은 명령어를 실행해보자

  ```text
  rake db:migrate
  rake db:test:load
  ```

## 8.3 Unit 테스트

### 8.3.0 Unit 테스트

* `Unit 테스트(단위 테스트)`는 애플리케이션을 구성하고 있는 라이브러리\(주요 모델 등\)가 제대로 작동하는지 확인하는 테스트
* Rails에서 실행하는 테스트 중에서도 가장 기본적인 테스트다.

### 8.3.1 Unit 테스트 기본

* 책의 `3.7.2` 절을 잘 따라했으면 `/test/models` 폴더 내부에 테스트 스크립트로 `book_test.rb`가 생성되어 있을 것임 -- 어떻게 하면 생기는지 확인해보자
* `book_test.rb`의 첫 모습은 아래와 같다.

  ```ruby
  class BookTest < ActiveSupport::TestCase
    # test "the truth" do
    #   assert true
    # end
  end
  ```

  * 주석으로 표시된 부분이 테스트를 어떻게 하는지에 대해 간단히 설명해 주는 부분이다.
  * 먼저 `test` 메서드를 정의해줘야 한다.
    * `test` 메서드의 매개 변수는 `test 이름`과 `&block` 이다.
    * `test이름`은 말 그대로 어떤 테스트의 이름이고
    * `&block` 은 테스트에 넘겨줄 실행 블록을 의미한다.

* 간단한 테스트를 만들어보자. 우리가 만들 테스트는 아래와 같다.

  1. `book` 레코드를 하나 만든다.
  2. `book`을 `save` 한다.
     * 만약 `save`에 실패하면 `Test`의 `Fail`
     * 아래서  사용된 `assert` 메서드는 종류가 무진장 많다. 테스트의 종류에 맞춰서 사용하면 된다. 자세한 정보는 [여기](http://docs.seattlerb.org/minitest/Minitest/Assertions.html)에서 알아보자.

  ```ruby
  class BookTest < ActiveSupport::TestCase
    test "book save" do
      book = Book.new({
                        isbn: '978-4-7741-4466-X',
                        title: 'Ruby on Rails 입문',
                        price: 3100,
                        publish: '제이펍',
                        published: '2014-02-14',
                        cd: false
                      })
      assert book.save, 'Failed to save'
    end
  end
  ```

* 이후 터미널에서 테스트를 진행하자!

  ```text
  rake test:models # 모든 테스트 실행
  rake test:models TEST="test/models/book_test.rb" # 특정 테스트만 실행
  ```

* 만약 `book.save` 가 실패한다면 터미널에 실패했다는 표시인 `F`와 함께 `assert` 의 두 번째 인자로 준 `msg`가 출력이 된다.
* 테스트 메서드가 여러 개 있을 때는 실행 순서가 마음대로다. _**따라서 실행순서에 의존하는 테스트 메서드는 작성하지 말자!**_

### 8.3.2 Unit 테스트 구체적인 예제

#### 모델 유효성 결과 확인

> 유효성 검사 오류 정보는 `errors` 메서드로 접근할 수 있다!

* 앞서서 간단한 저장 테스트를 해봤다.
* 이번에는 모델에서 정의된 유효성 검사의 동작인 확인해보자. 아래와 같이 스크립트를 작성하자 이 스크립트는 고의적으로 생성한 `book`이 유효성 검사를 통과하지 못하게 만드는 스크립트다.
* ```ruby
    test "book vaildate" do
      book = Book.new({
                        isbn: '978-4-7741-44',
                        title: 'Ruby on rails 입문',
                        price: 3100,
                        publish: '제이펍',
                        published: '2014-02-14',
                        cd: false
                      })
      assert !book.save, 'Failed to Validate!'
      assert_equal book.errors.size, 2, 'Failed to Validate count'
      assert book.errors[:isbn].any?, 'Failed to isbn validate'
    end
  ```

  1. `assert !book.save, 'Failed to Validate!'`
     * 유효성 검사를 통과하지 못해, 모델 저장에 실패했을 경우
  2. `assert_equal book.errors.size, 2, 'Failed to Validate'`
     * `assert_equal` 은 첫 번째, 두 번째 인자를 비교해서 만약 값이 같으면 `true`를 반환하고 아니라면 `false`를 반환한다.
     * 두 개의 유효성 검사가 있을 경우
  3. `assert book.erros[:isbn].any?, 'Failed to isbn validate'`
     * isbn 필ㄷ의 윻성 검사 오류가 적어도 한 개 이상 있을 경우

#### 모델을 통해 검색한 결과를 확인

* 모델을 통해 검색했을 때, 정확한 결과를 추출하는지 확인해보자
* `Book` 모델에서 `title 필드`를 키로 `자바스크립트 라이브러리 실전 활용`을 검색했을 때 다음을 확인한다.
  1. 추출한 결과가 Book 객체인지 확인
  2. isbn 필드가 픽스처 `books.yml`에 있는 `:jslib` 키의 isbn 필드와 같은지 확인
  3. publish 필드가  `2013/03/19` 인지 확인
* ```ruby
     test "where method test" do
      result = Book.find_by(title: '자바스크립트 라이브러리 실전 활용')
      assert_instance_of Book, result, 'result is not istance of Book!'
      assert_equal books(:jslib).isbn, result.isbn, 'isbn column is wrong.'
      assert_equal Date.new(2013, 03, 19), result.published, 'published column is wrong.'
    end
  ```

  > Rails는 테스트를 실행할 때 픽스처를 데이터베이스에 로드하는 것뿐만 아니라, 테스트 스크립트에서 이용할 수 있게 해시로 전개해준다!

  * 따라서 `books.yml` 내부에 `:jslib` 이라는 키로 정의된 레코드가 있다면, `books(:jslib)`에 접근할 수 있다.
  * 그리고 모델 객체를 리턴하므로 그대로 isbn 속성에 접근할수도 있다.
  * 이러한 기능덕분에 `assert_equal books(:jslib).isbn` 같은 코드를 작성할 수 있는것.
  * `books.yml`에서 `jslib`는 아래와 같다.

    ```text
    jslib:
      id: 2
      isbn: 978-4-7741-5611-8
      title: 자바스크립트 라이브러리 실전 활용
      price: 27090
      publish: 제이펍
      published: 2013-03-19
      cd: false
    ```

#### 뷰 헬퍼 테스트 -- 실습실패

* 뷰 헬퍼 테스트도 기본적인 방법은 모델 테스트와 동일하다.
* `2.2.1절` 에서 컨트롤러를 순서대로 생성했다면, `/test/helpers` 폴더 내부에

#### 테스트 준비와 뒤처리 - setup과 teardown

* 테스트 스크립트에는 각각의 테스트 메서드가 호출되기 전과 후에 자동으로 호출되는 메서드가 있따.
* 테스트 스크립트의 부모 클래스 `ActiveSupport::TestCse`에 정의되어 있는 메서드 이므로 오버라이드해서 사용한다.
  * | 메서드 | 설명 |
    | :--- | :--- |
    | setup | 각 테스트 메서드가 호출되기 전에 실행\(사용할 리소스 초기화\) |
    | teatdown | 각 테스트 메서드가 호출된 이후에 실행\(사용한 리소스 뒤처리\) |
* 앞서 진행했던 `where method test`를 `setup`과 `teardown`으로 수정해보자
* ```ruby
  def setup
    @b = books(:jslib)
  end
  def teardown
    @b = nil
  end

  test "where method test" do
    ....
      assert_equal @b.isbn, result.isbn, "isbn column is wrong."
        assert_equal @b.published, result.published, "published column is wrong"
    ...

  end
  ```

## 8.4 Functional 테스트

> Gemfile 에 `rails-controller-testing`을 추가해주자

* `Function 테스트`는 컨트롤러의 동작 또는 템플릿의 출력을 확인하기 위한 테스트다.
* `Functional 테스트`에서는 브라우저처럼 `HTTP` 요청을 유사적으로 작성하는 것으로 액션 메서드를 실행하고 그 결과로 `HTTP` 상태코드, 템플릿 변수 또는 최종적인 출력 구조 등을 확인한다.
* 또한 라우트 정의의 유효성을 확인하는 것도 `Functional테스트`  의 역할이다.

### 8.4.1 Functional 테스트 기본

* `/test/controllers` 폴더 내부에 테스트 스크립트로 존재한다.
* `hello_controller_test.rb`룰 수정해서 `hello` 컨트롤러를 테스트 하자. 아래처럼 스크립트를 작성하자

  ```ruby
  # /test/controllers/hello_controller_test.rb
  class HelloControllerTest < ActionDispatch::IntegrationTest
    test "list action" do
      get hello_list_path # 책에 있는대로 URL이 틀려서 안됨.
      assert_equal 10, assigns(:books).length, 'found rows is worng.'
      assert_response :success, 'list action failed.'
      assert_template 'hello/list'
    end
  end
  ```

* 위 코드를 분석해보자

#### 1. Get 메서드로 요청 생성

* Functional 테스트에서는 일단 컨트롤러를 실행하기 위해 `get` 메서드로 `HTTP` 요청을 생성한다.
* ```ruby
  get(action [.params [session [,flash]]])
  # action: 실행할 액션
  # params: 실행 시에 함께 전달할 매개 변수
  # session: 실행 시에 이용할 세션 정보
  # falsh: 실행 시에 사용할 플래시 정보
  ```
* 현재 코드에서는 실행할 액션만 지정하고 있지만 다음과 같이 인자로 전달할 수 있다.

  ```ruby
  get :show, { id: 100 }

  # 아래는 세션
  post login_path, params: { session: { email: user.email, password: password} }
  ```

  * 세션을 보내는 방법은 [여기](https://stackoverflow.com/questions/44461101/accessing-session-in-integration-test)를 참고하자.

* `get` 메서드가 아닌 `POST, PUT` 등등도 다 사용할 수 있ㄷ.

#### 2. Functional 테스트에서 이용할 수 있는 예약 변수

* `Functional` 텥스트에서는 `get, post` 등의 메서드를 사용한 후에 아래의 표처럼 예약 변수에 접근할 수 있다.
  * | 분류 | 변수 이름 | 설명 |
    | :--- | :--- | :--- |
    | 객체 | @controller | 요청을 처리한 컨트롤러 클래스 |
    |  | @request | 요청 객체 |
    |  | @response | 응답 객체 |
    | 해시 | assigns\(:key\), assigns\['key'\] | 뷰에서 사용할 수 있는 템플릿 변수 |
    |  | cookies\[:key\] | 쿠키 정보 |
    |  | flash\[:key\] | 플래시 정보 |
    |  | Session\[:key\] | 세션 정보 |

    이러한 변수를 통해 액션 메소드의 내부에서 생성된 정보에 접근하자
* 위의 테스트에서 `assert_equal 10, assigns(:books).length, 'found rows is worng.'` 부분을 보면 `assigns(:books)` 가 의미하는 바는 `hello/list` 의 템플릿 변수 `@books`를 추출하는 과정이다. 그리고 이 `@books`의 사이즈를 `10`과 비교하는 테스트다.

#### 3. Functional 테스트에서 사용할 수 있는 assert\_xxxxx 메서드

| 메서드 | 설명 |
| :--- | :--- |
| assert\_difference\(exp \[,diff \[,msg\]\]\) { block } | 블록 내부의 코드를 실행한 후에 exp가 diff만큼 변화했는지\(매개 변수 diff의 기본값은 1 이다\) |
| assert\_no\_difference\(exp \[,diff \[,msg\]\]\) { block } | 블록 내부의 코드를 실행한 후에 식 exp가 변화하는지 |
| assert\_generates\(exp\_path, options \[,defaults ,extras \[,msg\]\]\) | 주어진 매개 변수 options\(url\_for 메서드의 매개 변수와 동일\)에 들어갔을 때 exp\_path가 생성되는지 확인 |
| assert\_recognizes\(exp\_options, path \[,extras \[,msg\]\]\) | 주어진 path로 매개 변수 exp\_options가 실행되는지\(assert\_generates의 반대\) |
| assert\_response\(type \[,msg\]\) | 지정된 HTTP 상태가 리턴되는지\(매개변수 type은 :success/\(200\), redirect\(300대\), :missings\(404\), :erros\(500대\) 등\) |
| assert\_redirected\_to\(\[opts \[,msg\]\]\]\) | 리다이렉트 대상 경로가 opts가 맞는지 |
| assert\_template\(temp\[,msg\]\) | 지정된 템플릿이 호출되는지 |
| assert\_select\(se;ectpr \[,equality \[,msg\]\]\) | 선택자 selector로 선택되는 요소의 내용이 매개 변수 equality인지 |
| assert\_select\(element, selector \[,equality \[,msg\]\]\) | 요소 element 내부에서 선택자 selector로 요소를 추출하고, 해당 요소의 내용이 매개 변수 equality인지 |

* `assert_response :success, 'list action failed.'` 이 코드는 테스트 결과 response가 200인지 확인하는 코드다.
* `assert_template 'hello/list'` 는 `get hello_list_path`의 결과 `hello/list` 뷰 템플릿이 잘 호출되는지 확인한다.

### 8.4.2 Functional 테스트에서 사용할 수 있는 Assertion 메서드

* 각 Assertion 메서드를 사용하는 법을 확인해보자

#### assert\_difference 메서드

> 처리 후의 상태 변화를 확인한다.

* `assert_difference` 메서드는 블록 내부의 처리를 실행했을 때, 값이 변화되는지를 확인하는 Assertion 메서드다.
* ```ruby
  # books_controller_test.rb
    test "diff check" do
      assert_difference 'Book.count', 1 do
        post books_path, params: { book: { isbn: '978-4-7741-4223-1',
                              title: 'Ruby 포켓 레퍼런스',
                              price: 3000,
                              publish: '제이펍',
                              published: '2014-03-14',
                              cd: false}
        }
      end
    end
  ```

  * 책에 있는 그대로 하면 실행되지 않는다. `post` HTTP메서드를 보낼 `url`을 `books_path`로 수정해주자.
  * `book` 생성을 `params`으로 감싸준다.
* 위의 스크립ㅌ는 `create` 액션을 수행하고 주어진 포트스 데이터를 기반으로 도서 정보 하나를 생성, 추가한다.
* 따라서 `create` 액션이 정상적으로 완료되면 `books` 테이블의 데이터 개수가 1만큼 증가할 것이다.
  * 기존에는 10, create가 성공하면 11
* 이전의 `Books.count` 와 성공 이후의 `Books.count`는 1만큼 차이가 날 것임.
* 만약 실패하면 카운트가 증가하지 않고 그대로 10. 테스트는 실패한다.
* 만약 변화하지 않는걸 테스트하기 위해서는 `assert_no_difference` 로 하면 된다.

#### assert\_generates

> 라우팅 동작 확인

* `assert_generates` 메서드는 두 번째 매개 변수로 지정한 컨트롤러와 액션이 첫 버째 매개 변수에 입력한 경로로 들어갔을 때 실행되는지 확인한다.
* 두 번째 매개 변수에는 `url_for` 메서드의 매개 변수처럼 내용의 해시를 지정할 수도 있다.
* ```ruby
    test "routing check" do
      assert_generates('hello/list',
      {controller: 'hello', action: 'list'})
    end
  ```
* 위와 같은 스크립트는 `localhost:3000/hello/list`에 접근했을 때 아래를 확인한다.
  * `hello` 컨트롤러의
  * `list` 액션 메서드가 실행되는지

#### assert\_recognizes

> 컨트롤러의 액션메서드가 지정한 라우트로 들어가는지 확인한다.

```text
  test "recognizes" do
    assert_recognizes({ controller: 'hello', action: 'list'},
                        'hello/list')
  end
```

#### assert\_select

> 템플릿 출력 결과를 확인한다

* 이 메서드는 뷰의 출력 결과를 확인하기 위한 메서드다. 다양한 테스트를 할 수 있다.
* ```ruby
  assert_select(selector [,equality [,msg]])
  # selector: 선택자
  # equality: 비교 내용
  # msg: 오류가 발생할 때 출력할 글자
  ```
* 매개 변수 `selector`에는 `CSS` 선태자를 지정한다.
* 이 메서드에는 선택자로 `HTML` 요소에 간단히 접근할 수 있다는 것이 장점
* 아래는 `assert_select` 메서드에서 사용할 수 있는 선택자들이다

  * 홀수줄에 나오는 게 선택자, 의미
  * 짝수줄에 나오는게 바로 위의 선택자, 의미의 예시다.

  | 선택자 | 의미 |
  | :--- | :--- |
  | 작성 예 | 예의 의미 |
  | \#id | 지정된 id 값을 가진 요소를 선택 |
  | \#result | id="result"인 요소 |
  | .class | 지정된 class 속성을 가진 요소 선택 |
  | .article | class="article" 요소 |
  | element | 지정된 태그 요소 선택 |
  | script | 모든  요소 |
  | element.class | 지정된 class 속성을 가진 요소 element 선택 |
  | div.list | class="list" 인  요소 |
  | ancestor descendant | 요소 ancestor 내부에 있는 모든 자손 요소 descendant 선택 |
  | div.main ul | class="main"인  태그 내부의 모든  요소 |
  | parent &gt; child | 요소 parent 바로 아래에 있는 요소 child 선택 |
  | \#menu &gt; ul | id="menu" 인 요소 바로 아래의  요소 |
  | \[attr=value\] | 속성 attr의 값이 value인 요소 선택 |
  | img\[src="logo.gif"\] | src속성이 logo.gif인  요소 |
  | :first-child | 자식 요소 중의 첫 번째 요소 선택 |
  | ul\#menu li:first-child |  요소 아래의 첫  요소 |

* HTML 문서에서 특정한 요소를 추출하여 이를 어떤 방식으로 테스트할 것인지를 나타내는 것이 매개 변수 `equality`이다. 아래는 구체적인 사용 방법이다.
* ```ruby
    test "select check" do
      get hello_list_path
      # <title> 태그가 한 개 이상 존재하는지 확인
      assert_select 'title', true

      # <title>태그가 한 개 이상 존재하는지. 위와 동일
      assert_select 'title', true

      # <font> 태그가 존재하지 않는지
      assert_select 'font', false

      # <title> 태그 내부에 글자 [Railbook] 이 있는지
      assert_select 'title', 'Perfect'
      #

      # # <title> 태그 내부의 글자가 영어와 숫자로 구성되어 있는지
      assert_select 'title', /[A-Za-z0-9]+/
      #
      # # <script> 태그의 data-turbolinks-track 속성이 비어있지 않은지
      # assert_select 'script[data-turbolinks-track=?]', /.+/

      # <table> 태그 내부에 style 속성을 가진 <tr> 태그가 10개 존재하는지 확인
      # assert_select 'table tr[style]', 10

      # <table> 태그 내부에 <tr> 태그가 11개 존재하는지 확인
      assert_select 'table tr', 11

      # <table> 태그 내부에 <tr> 태그가 1 ~ 11개 존재하는지 확인
      assert_select 'table tr', 1..11

      # <title> 요소가 한 개만 존재하고 글자가 Perfect인지 확인
      assert_select 'title', { count: 1, text: 'Perfect' }

    end
  ```
* 지정할 수 있는 값들은 아래에 표를 참고하자

| 값 | 확인하는 내용 |
| :--- | :--- |
| True, 생략 | 지정된 요서가 한 개 이상 존재하는지 |
| false | 지정된 요소가 존재하지 않는지 |
| 문자열 | 지정된 요소 내부의 글자에 문자열이 있는지 |
| 정규 표현식 | 지정된 요소 내부의 글자를 정규 표현식으로 나타낼 수 있는지 |
| 정수 | 선택된 요소의 선택된 요소의 개수가 지정된 수와 같은지 |
| Range 객체 | 선택된 요소의 개수가 지정된 범위에 있는지 |
| 해시 | 지정된 여러 개의 조건으로 요소를 확인 - 지정 가능한 키는 아래의 표를 확인 |

| 키 | 확인하는 내용 |
| :--- | :--- |
| text | 문자열 또는 정규표현식 일치 여부 |
| html | 문자열 또는 정규 표현식을 HTML 문자열로 확인 |
| count | 일치하는 요소의 개수가 같은지 |
| minimun | 일치하는 요소의 개수가 최저 지정 수 이상인지 |
| maximun | 일치하는 요소의 개수가 최대 지정 수 이하인지 |

## 8.5 Integration 테스트

> 이 테스트는 책의 예시가 아닌 내가 간단하게 만든 사이트로 할것임

* `Integration 테스트`는 _**통합 테스트**_라고도 부른다.
* _**여러 개의 컨트롤러를 넘나들며 실제  사용자의 행동을 모방할 때 사용한다.**_
* 아래의 순서로 테스트 할 것임.
  1. `get new_post_path`로 포스트 생성에 접근
  2. 로그인이 안되었으면 자동으로 `login/index`로 접근해야함
  3. 로그인 페이지에서 사용자 이름과 비밀번호를 입력하고 인증 처리
  4. 로그인에 성공하면 `posts_path`로 리다이렉트

```ruby
class AdminLoginTest < ActionDispatch::IntegrationTest
  # test "the truth" do
  #   assert true
  # end

  test "login test" do
    # 1. new_post_path 로 접근
    get new_post_path
    # 응답이 리다이렉트 되었는지 확인
    assert_response :redirect

    # 리다이렉트 대상이 login#index 인지 확인 (컨트롤러, 액션 직접 지정해도 되고, url_helper를 입력해도 됨)
    # assert_redirected_to controller: :login, action: :index
    assert_redirected_to login_index_path

    # flash[:referer]에 현재 URL이 설정되었는지 확인
    # assert_equal 'hello/view', flash[:referer] # 이건 책의 예시


    # 2. 로그인 페이지 출력 확인
    follow_redirect! # 중요!
    # 응답이 성공인지 확인
    assert_response :success

    # 3. 사용자 이름/비밀번호를 입력해 인증 처리
    post login_auth_path, params: {username: 'sanam', password: '123456'}

    # 응답이 리다이렉트 되었는지 확인
    # assert_response :success
    assert_response :redirect
    assert_redirected_to posts_path
  end
end
```

* 위의  스크립트는 _**3번에서 뭔가 잘못작동한다!**_
* 테스트결과 로그인을  실패하고 내가 의도한곳으로 `redirect` 되지 않는다. 실패원인 찾아볼것!

