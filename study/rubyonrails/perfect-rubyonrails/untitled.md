# Untitled

## 3.1 스캐폴딩 기능으로 어플리케이션 개발

### 3.1.0 스캐폴딩이란?

* 스캐폴딩이란 `기반` 이라는 의미로, 기본 기능을 미리 구현한 어플리케이션의 골격을 생성하기 위한 기능이다.
* 자동으로 생성되었다고는 해도 코드를 많이 수정해줘야한다. 그래도 아래와 같은 장점들이 있다.
  1. 일단 동작하는 어플리케이션을 만들고 싶을 때
  2. 데이터 우지 관리 등의 레이아웃을 열심히 만들 필요가 없는 페이지를 대량으로 만들어야 할 때
  3. Rails의 기본적인 CRUD 구현을 이해하고 싶을 때

### 3.1.1 스캐폴딩 개발 순서

#### 1. scaffold

```text
rails generate scaffold name field:type ... [options]
```

* `name` 에는 모델의 이름이 들어간다.
* `CoC`에 따라 컨트롤러, 뷰 등의 이름은 형식에 맞게 지어진다.

#### 2. 마이그레이션 파일 실행

```text
rake db:migrate
```

* 이 명령을 실행하면 그제서야 애플리케이션에 우리가 만든 모델이 적용이 된다.
* `migrate`는 이전에 db를 생성하거나 수정할 때 사용한다고 했었다.
  * 지금은 `생성`의 단계에서 사용함.

### 3.1.2 자동 생성된 라우트 확인 -- resource 메서드

* 스캐폴딩 기능으로 애플리케이션을 생성하면 `config/routes.rb` 에 자동으로 라우트가 추가된다.
* 이 때 눈여겨 볼 것은 이전에는 route 설정을 해줄 때 하나하나 설정을 해줬는데 `resource` 단 한줄만 사용했다는 것.
* [resource](https://guides.rubyonrails.org/routing.html#1.3) 공식 문서를 보고 이해하자.
* 터미널에 `rails routes` 를 입력하면 각 요청 주소를 처리하는 메서드를 확인할 수 있다.
  * 또는 주소창에 `/rails/info/routes`를 입력하자

## 3.2 목록 화면 자성

### 3.2.1 index 액션 메서드

* `index` 액션 메서드는 `@posts = Post.all` 이라는 변수를 하나 생성한다. 이 변수는 `Post.all` 이라는 명렁으로 `post` 객체 전부를 뜻한다는 걸 알 수있다. 그리고 이 이름 자체도 중요하다. 이름이 `posts`처럼 복수형으로 되어있는것으로 보아 이 데이터는 `하나의 레코드`가 아닌 `하나의 테이블`이라는 걸 짐작할 수 있어야함.
* 이 액션 메서드에 대응되는 템플릿은 `index.html.erb` 파일이다. 하지만 이전에는 보지 못했던 `index.json.builder` 라는 파일이 있다. 이 파일은 액션의 결과를 `JSON` 형식으로 출력할 때 사용하는 것. 만약 JSON 파일을 원한다면 URL에 `.json` 을 붙여주자.

#### `respond_to`

* 방금 `view` 폴더에 html 파일이 아닌 json 파일이 있었다. 그렇게 되면 액션 메서드에서 이 부분에 대한 분기 처리를 해줘야 한다. 즉 반복이 일어난다는 것. 그리고 ROR 은 이 반복을 너무 싫어한다. 따라서 URL 포맷에 따라 자동으로 요청 분기를 해주는 메써드가 존재한다. 바로 `respond_to` 이 함수의 사용법은 아래와 같다.

```ruby
  def index
    @books = Post.all
    respond_to do |format|
      format.html # index.html.erb
      format.json { render json: @books}
```

### 3.2.2 index.html.erb 템플릿

```ruby
<td><%= link_to 'Show', post %></td>
<td><%= link_to 'Edit', edit_post_path(post) %></td>
<td><%= link_to 'Destroy', post, method: :delete, data: { confirm: 'Are you sure?' } %></td>
```

* 코드를 보면 이전에 보지 못했던 `link_to` 가 등장한다. 이 메서드는 `뷰 헬퍼`의 메서드다.

#### 뷰 헬퍼

* 뷰헬퍼란?
  * 뷰 헬퍼란 템플릿 파일을 작성할 때 도움을 주는 메서드를 의미한다.
* 뷰헬퍼를 사용하면 자주 사용되는 처리를 손쉽게 해낼 수 있다.
* `link_to` 또한 매우 자주  사용되는 뷰 헬퍼다. 이 헬퍼 메서드는 `html`의  `a`  태그에 대한 처리를 손쉽게 만들어 준다. 아래처럼 사용된다. \(ROR를 조금 공부해본바로 메써드가 있을 때 괄호를 사용하지 않는다...?\)

```ruby
link_to(body, url, [, html_options])
    # body: 링크 텍스트
    # url : 링크 대상 경로 또는 매개 변수 정보
    # html_options: <a> 태그에 적용할 옵션 정보("속성":"값)
```

#### `link_to` 메서드로 특정한 경로 표기

* ```ruby
  <%= link_to 'Show', book %>
  ```

  ​

* 위와 같은 코드가 있다고 하자. `Show` 는 당연히 기본적인 텍스트. 그렇다면 `book`은 뭐지?
  * 링크 대상 경로가 아닌 `매개 변수 정보` 다.
  * 링크 대상 경로에 모델 객체를 적용하는 Rails는 객체를 나타내는 `id 속성`을 사용한다.
    * 즉 여기서는 `book` 이라고 하나만 썼지만 실제로는 `/book/1` 과 같은 효과를 내는것.

#### `link_to` 메서드로 특정한 경로 표기

```ruby
<%= link_to 'Edit', edit_post_path(book) %>
```

* 위 코드를 분석해보자. 위 코드의 `edit_book_path(book)` 는 도대체 무엇을 의미하는 걸까? 이를 알기 위해서는 터미널에 `rails routes | grep 모델이름` 를 입력하자 아래와 같은 결과가 출력됨.
* ```text
  root                GET    /                                              posts#index
  posts                GET    /posts(.:format)                         posts#index
                          POST   /posts(.:format)                   posts#create
  new_post        GET    /posts/new(.:format)               posts#new
  edit_post        GET    /posts/:id/edit(.:format)    posts#edit
  post                GET    /posts/:id(.:format)         posts#show
                          PATCH  /posts/:id(.:format)         posts#update
                          PUT    /posts/:id(.:format)         posts#update
                          DELETE /posts/:id(.:format)         posts#destroy
  ```
* `edit_post_path` 란 `edit_post`의 `URL` 인 `/posts/:id/edit` 를 의미함.

#### 링크를 클릭할 때 확인 대화상자 표시

```ruby
<%= link_to ... , data: { confirm: 'Are you syre?' } %>
```

#### 링크 대상에 HTTP GET 이외의 메서드로 접근

```ruby
<%= link_to ... , method: :delete ... %>
```

## 3.3 상세 화면 작성

### 3.3.1 `show` 액션 메서드

### `before_action`

* ```ruby
  before_action method, only: action
      # method: 필터로 실행되는 메서드
      # action: 필터를 적용할 액션 메서드(배열)
  ```
* `before_action` 메서드는 어떤 특정 액션 메서드가 실행될 때 만약 `before_action` 으로 지정되어 있다면 `먼저` `before_action` 을 실행하고 이후에 특정 액션 메서드를 실행함을 의미한다.
* 주로 `private`로 만든다.

### 3.3.2 `show.html.erb` 템플릿 파일

* 여기서는 그다지 볼건 없고... 가장 위의 `notice` 만 보자.
* 일반적으로 데이터를 `show` 버튼을 누르고 봤을 때는 `notice` 는 동작하지 않는다. 왜냐하면 액션 메서드의 인자로 notice 가 들어가지 않았기 때문.
* 그러면 언제 `notice` 가 동작하냐?
  * `create` 또는 `update`가 되었을 때

## 3.4 새로운 데이터 등록 화면

### 3.4.1 `new.html.erb` 템플릿 파일

* 템플릿 파일은 단 세줄이다. 두번째 라인의 `<%= render 'form'` %&gt; 만 알면 된다.
* `render form` 을 통해서 렌터링 하는 html 파일은 `_form.html.erb` 파일이다.
* 이 파일은 `부분 템플릿(partial template)` 라고도 부른다. 누군가의 부분이 되기 때문이다.
* 이미 짐작했겠지만 이 파일의 존재 의의는 중복을 없애기 위함이다.
* 지금 현재 스캐폴딩으로 생성했을 때는 `new`와 `edit` 페이지가 완전히 동일하다. 만약 이 페이지를 따로 만든다면 그건 중복이 된다. 따라서 `render` 메서드를 통해 중복적인 부분을 만들어 놓은 파일을 렌더링 하는 것.
* 부분 템플릿 파일은 `_` 로 이름이 시작해야한다.

#### `_form.html.erb`

* ```ruby
  <%= form_with(@book) do |f| %>
  ```
* `form_with` 메서드는 모델과 연동되는 입력 양식을 생성할 때 사용된다.
* 모델과 연동된다는 말은 인자로 들어오는 `book` 객체가 초기화 되었는지 여부를 의미한다. 모델과의 연동 여부가 중요한 이유는 이 부분 템플릿을 사용하는 액션 메서드, view 가 `new`와 `edit` 이기 때문이다. 즉 두 가지 다른 액션 메서드에서 사용하므로 두 경우에는 분명한 차이가 생겨야 한다는 것이다.
* 어쨋든 이 메서드의 결과로 `new` , `edit` 각각에서 이 부분템플릿을 `render` 했을 때는 분명한 차이가 생긴다.

#### 모델의 속성에 대응되는 입력 양식 위치 지정

* ```text
  <%= f.text_field :title %>
  ```
* 위 코드를 분석하면 `Book 객체를 수정하는 입력 양식에서 title 필드에 대응되는 텍스트 박스를 생성한다` 이다.

### 3.4.2 new와 create 액션 메서드

* 새로운 데이터 등록 페이지는 두 개의 액션과 연결되어 있다.
  1. 첫 번째는 `new`
  2. 두 번째는 `create`
* `new`는 입력 양식을 출력한다.
* `create`는 `new` 의 입력양식에서 버튼을 누르면 시행되는 ㅐㄱ션이다.
  * 데이터 등록 처리를 한다.

#### 입력 양식의 내용을 기반을 객체 생성

* ```ruby
  def new
    @post = Post.new
  end
  ```
* `new` 액션은 비어있는 `Book` 객체를 생성한다. 이때 생성되는 객체로 템플릿 파일에서 모델의 속성을 알 수 있다. \(`form_with`\)

#### POST 요청으로 보내진 데이터  추출\(book\_params 메서드\)

* ```ruby
  def post_params
    params.require(:post).permit(:title, :body)
  end
      # :post -> 모델 이름
      # :title, :body -> 칼럼 이름
  ```
* 입력 양식으로 \(`버튼`\)부터 받은 데이터\(`POST 데이터`\) 를 추출할 때 사용하는 코드다.
* 이렇게 하면 `post`라는 이름이 붙은 입력 양식 데이터 중에서 우리가 `permit` 의 인자로 지정한 속성만 가져와서 `params`로 지정한다.

#### 입력 값으로 모델 재구성

* ```ruby
  def create
    @post = Post.new(post_params)

    respond_to do |format|
      if @post.save
        format.html { redirect_to @book, notice: 'Book was created'}
        format.json { render action: 'show', status: :unprocessable_entity}
      else
        ...
      end

    end
  ```
* 위에서 만든 `post_params` 를 실행하고 그 `return` 값인 `params`를 새로운 post 객체로 만든다.
* 그리고 이 객체를 `save` 함으로써 db에 저장함
* 만약 `save` 에 성공했을 경우 요청 포맷에 따라 `html`, `json`의 두 가지 분기가 생긴다.
  * `html`: `@book` 으로 `redirect_to` 된다.
  * ```ruby
    redirect_to url [, option]
        # url: 리다이렉트 경로
        # options: 옵션
    ```
  * 

