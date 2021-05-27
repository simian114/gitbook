# passport를 활용한 로그인

## Passport 를 이용해서 로그인을 구현하자

> 배우기는 매우 어렵지만... 사용하기는 너무 쉽다! 따라서 한번쯤은 배워볼만하다!!!!!!

생활코딩을 보고 학습하자

## id, password와 같은 고전적인 방법으로 먼저 로그인을 해보자

> 이런 방법을 local 전략이라고 한다.
>
> [http://www.passportjs.org/docs/username-password/](http://www.passportjs.org/docs/username-password/) 를 참고하자
>
> [http://www.passportjs.org/docs/authenticate/](http://www.passportjs.org/docs/authenticate/) 로 authenticate 미들웨어를 넣자1!!! 2개가 있는데 두 번째 방법을 사용하자

#### 설치하기

```text
npm install passport
npm install passport-local
```

### 로그인 프로세스

```javascript
app.post('/author/login',
  // 기존에는 여기서 이메일을 검증하고,.. 하는 과정이 있었다!!
  passport.authenticate('local', {
      successRedirect: '/author',
        failureRedirect: '/author/login'
    }
));
```

`/author/login` 으로 들어오면 `passport` 가 미들웨어로서 동작하게 한다.

로그인이 성공하면 `/author` 가고 실패하면 `/author/login` 으로 다시 오게된다.

### 여기서 드는 의문

그러면 이제 `/author/login` 여기의 라우팅에서 처리하던 자격인증 코드들을 `passport` 는 **도대체 어떻게** 처리해주는거지?

## passport 에서 자격증명을 하는 방법

> 완전 자동으로 해주는건 없다!! 우리가 직접 설정을 해야한다.

[여기를 보면 어떻게 자격증명을 해야하는지 나와있다!!](http://www.passportjs.org/docs/configure/)

**결론은 우리가 이전에 하던 작업들을 middleware 로서 먼저 등록해놓는다!!!!!!!**

```javascript
var passport = require('passport')
  , LocalStrategy = require('passport-local').Strategy;

passport.use(new LocalStrategy(
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      if (!user.validPassword(password)) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      // 여기까지 오면 성공!! 두 번째 인자로 찾은 유저를 리턴해주자!!!!
      return done(null, user);
    });
  }
));
```

### 이렇게 동작하기 위해서는 브라우저에서 보내줄 때 형식에 맞춰 보내줘야한다!!

[여기를 보면 FORM을 보낼 때 어떻게 보내야 하는지 나와있다. FORM을 보자 조금 아래에 있다.](http://www.passportjs.org/docs/username-password/)

요약하자면 아이디는 `username` 으로 보내고 비밀번호는 `password` 로 보내야 하는거

#### 하지만 우리는 username 이 아니라 email 이잖아?

아래로 조금만 내리면 `parameters` 가 나오는데 여기를 보고 수정하면 된다.

```javascript
passport.use(new LocalStrategy({
      // 이거를 해주면 된다!!!!1
    usernameField: 'email',
    passwordField: 'passwd'
  },
  function(username, password, done) {
    User.findOne({ username: username }, function (err, user) {
      if (err) { return done(err); }
      if (!user) {
        return done(null, false, { message: 'Incorrect username.' });
      }
      if (!user.validPassword(password)) {
        return done(null, false, { message: 'Incorrect password.' });
      }
      // 여기까지 오면 성공!! 두 번째 인자로 찾은 유저를 리턴해주자!!!!
      return done(null, user);
    });
  }
));
```

## 이렇게까지 하면 아직 뭔가 안된다!!!

**왜냐하면 passport.initialize** 를 설치하지 않았기 때문!!!!!!!!!!!!! 세션이랑 동시에 이니셜라이즈를 해주자

[http://www.passportjs.org/docs/configure/](http://www.passportjs.org/docs/configure/) 의 마지막 부분에 `middleware` 를 보면 아래의 코드가 있다. Passport 를 이니셜라이즈 해주자!!

```javascript
app.configure(function() {
  app.use(express.static('public'));
  app.use(express.cookieParser());
  app.use(express.bodyParser());
  app.use(express.session({ secret: 'keyboard cat' }));
  app.use(passport.initialize()); // 이거랑
  app.use(passport.session());    // 이거
  app.use(app.router);
});
```

위치는 크게 상관없지만 **passport.use** 전에 실행해주자!

## 하지만 이렇게 해도 안된다.

`Failed to serialize user into session` 이런 문구가 뜰 것임

[http://www.passportjs.org/docs/configure/](http://www.passportjs.org/docs/configure/) 의 `session` 부분을 보자

```javascript
app.use(passport.initialize()); 
app.use(passport.session());    
passport.serializeUser(function(user, done) {
    // 이 부분을 바꿔야한다!
  // done(null, user.id);
});

passport.deserializeUser(function(id, done) {
  // 이 부분도 바꿔야한다!
  //User.findById(id, function(err, user) {
  //  done(err, user);
  //});
});
```

### serializeUser, deserializeUser 로그인 과정의 처리를 해준다!!

이 메서드는 언제 호출될까? 로그인이 성공하면!

로그인이 성공하면 그 리턴 값이 여기의 첫 번째 인자로 오게 된다!!!!

Done 의 두 번째 인자로 식별할 수 있는 값을 주자 우리같은 경우는 `email` 을 주면 될 듯 하다.

`email` 을 두 번째 인자로 넘겨주면!!!! 이 값이 `deserializeUser` 메서드의 첫 번째 인자로 들어오게 된다!!!!!!!

#### 시리얼라이즈

> 세션스토에 로그인 정보를 저장한다.

로그인에 성공하면 이 메서드가 실행되고 세션에 식별자 값을 저장한다. 이후에는 페이지에 들어올 때 마다 디시리얼라이즈만이 실행된다.

#### 디시리얼라이즈

> 페이지에 들어갈 때 마다 이 메서드가 실행되면서 로그인 여부를 검사한다.

**페이지에 들어갈 때 마다 이게 실행된다!!!!!**

이 메서드는 이제 사용자가 브라우저로 올 때 마다 실행되는데, 자신에게 들어온 id 값을 이용해서 == 세션에 저장되어 있는 값임 DB에서 유저를 찾고, 로그인 성공 여부를 가른다!!

