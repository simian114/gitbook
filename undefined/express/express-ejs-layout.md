# express-ejs-layout 활용하기

참고한 링크

[https://github.com/Soarez/express-ejs-layouts\#readme](https://github.com/Soarez/express-ejs-layouts#readme)

[https://byungjinkim.tistory.com/31](https://byungjinkim.tistory.com/31)

[https://velog.io/@gidskql6671/Nodejs-express-ejs-layouts](https://velog.io/@gidskql6671/Nodejs-express-ejs-layouts)

## layout 사용하기

1. `npm install express-ejs-layouts` 를 설치한다
2. ```javascript
   const layout = require('express-ejs-layouts');
   app.use(layout);
   app.set('layout', 'layout/layout');
   ```

   로 `layout` 를 미들웨어로 추가한다. `set` 을 한 부분은 `layout` 폴더의 `layout` 파일을 사용하겠다는 선언이다.

3. `layout.ejs` 파일을 만들고 아래처럼 내용을 넣자

   ```text
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <meta http-equiv="X-UA-Compatible" content="ie=edge">
     <title>Document</title>
   </head>
   <body>
     <%- navbar %>
     <%- body %>
     <%- footer %>
   </body>
   </html>
   ```

4. 그리고 기존에 사용하던 `ejs` 파일에서는 아래처럼 만들면 된다. `contentFor` 을 사용함.

   ```text
   <%- contentFor('navbar') %>
   <p>
     this is navbar!!
   </p>

   <%- contentFor('body') %>
   <p>
     this is body~
   </p>

   <%- contentFor('footer') %>
   <p>
     this is footer...
   </p>
   ```

5. 이렇게 작성하고 페이지에 접속하면 잘 나온다.ㄴ

### 한발자국 더 나가기

위처럼 작성했을 때 만약 `ejs` 파일에서 `navbar`, `body,` `footer` 셋 중 하나라도 `contentFor` 로 넣어주지 않는다면 에러가 발생한다!!!

따라서 아무것도 들어가지 않았을 경우 에러가 발생하지 않게 수정을 해주자

### defineContent 선택적으로 ejs 파일 적용하기

> Optional Sections

`layout` 파일로 돌아가서 `defineContent` 를 사용해서 레이아웃을 잡아주자

```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <%- defineContent('navbar') %>
  <%- defineContent('body') %>
  <%- defineContent('footer') %>
</body>
</html>
```

이렇게 만들면 다른 view 파일에서 내용을 빠뜨려도 에러가 발생하지 않는다.

### script 를 넣어주는 방법

> 미들웨어를 조금만 set 해주면 아무곳에나 script 를 넣어도 찰떡같이 해준다.

아주 간단한 방법으로 `view` 파일의 하단에 `script` 를 넣어주는 방법을 생각할 수 있다.

그러나 이렇게 하면 문제가 될 수 있는게 `layout` 파일에 여러 개의 `view` 가 들어오는데 무작정 script 태그를 넣어주게 되면 아래의 사진처럼 `DOM` 중간에 `script` 태그가 섞이게 된다.

![DOM &#xD0DC;&#xADF8;&#xAC00; &#xC11E;&#xC784;](/Users/sanam/Desktop/Screen%20Shot%202021-05-24%20at%2010.12.28%20AM.png)

#### 해결

`express-ejs-layout` 에서 제공하는 방법을 사용하면 된다.

1. ```javascript
   const layout = require('express-ejs-layouts');
   app.use(layout);
   app.set('layout', 'layout/layout');
   app.set("layout extractScripts", true)
   ```

   기존의 코드에 `app.set("layout extractScripts", true)` 를 넣어준다. 내부에 들어가는 문자열에 띄어쓰기가 들어가있다고 해서 수정할 의문을 갖지말고 그냥 사용하면 된다.

2. `layout.ejs` 파일을 아래처럼 수정해주자

   ```text
   <!DOCTYPE html>
   <html lang="en">
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <meta http-equiv="X-UA-Compatible" content="ie=edge">
     <title>Document</title>
     <!-- bootstrap -->
     <link href="/vendors/bootstrap/css/bootstrap.min.css" rel="stylesheet">
     <link href="/stylesheets/author/common.css" rel="stylesheet">

     <!-- for desking -->
     <link rel="stylesheet" href="/stylesheets/author/index.css" />

   </head>
   <body>
     <%- defineContent('navbar') %>
     <%- defineContent('body') %>
     <%- defineContent('footer') %>

     <%- script %>
     <!-- bootstrap -->
     <script src="/vendors/bootstrap/js/bootstrap.bundle.min.js"></script>
   </body>
   </html>
   ```

3. 이제 view파일의 어느곳에 `script` 태그를 넣어도 `layout` 의 마지막에 `script` 가 생성된다. 결과는 아래와 같다.

![&#xC2A4;&#xD06C;&#xB9BD;&#xD2B8; &#xD0DC;&#xADF8;&#xAC00; &#xD558;&#xB2E8;&#xC5D0; &#xC704;&#xCE58;&#xD55C;&#xB2E4;.](/Users/sanam/Desktop/Screen%20Shot%202021-05-24%20at%2010.16.49%20AM.png)

### style 을 넣어주는 방법

> script와 다를게 없다.

1. ```javascript
   const layout = require('express-ejs-layouts');
   app.use(layout);
   app.set('layout', 'layout/layout');
   app.set("layout extractScripts", true);
   app.set("layout extractStyles", true);
   ```

   `app.set("layout extractStyles", true);` 를 추가한다.

2. `layout.ejs` 를 아래처럼 수정해준다.

   ```text
   ...
   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <meta http-equiv="X-UA-Compatible" content="ie=edge">
     <title>Document</title>
     <!-- bootstrap -->
     <link href="/vendors/bootstrap/css/bootstrap.min.css" rel="stylesheet">
     <link href="/stylesheets/author/common.css" rel="stylesheet">

     <!-- for desking -->
     <link rel="stylesheet" href="/stylesheets/author/index.css" />
     <%- style %>

   </head>
   ...
   ```

3. 이제 `ejs` 에서 `style.css` 를 그냥 넣어주면 끈

   ```text
   <%- contentFor('body') %>

   <link rel="stylesheet" href="/stylesheets/author/articles.css" />

   .....
   <section class="list-table">
       <table class="mytable">
         <thead class="table-header">
           <tr class="table-header__row">
             <th class="table-header__cell">No</th>
             <th class="tab
   ....
   ```

### 두 개 이상의 레이아웃 사용하기

[스택오버플로우 답변을 보자](https://stackoverflow.com/a/51917232)

