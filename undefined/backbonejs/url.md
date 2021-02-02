# 백본 컬렉션 URL에 쿼리 붙이기

## 컬렉

```javascript
export let Users = Backbone.Collection.extend({
  url: "/api/users/",
  model: User,
  parse: function (response) {
    return response.users;
  }
});

let users = new Users();

users.fetch() // get /api/users/
```

### 쿼리 하나만 날리기

```javascript
 // /api/users?status=online
users.fetch({ data: $param({ status: "online"}) });
```

### 쿼리 여러개 날리기

```javascript
let params = {
  "status": "online",
  "gender": "man"
};
// /api/users?status=online&gender=man
users.fetch( {data: $.param(params)} );
```

### 쿼리에 배열 보내기

```javascript
// /api/users?status[]=online&status[]=playing
users.fetch({data: $param({ status: ["online", "playing"]}) })
```


### 아래 두 페이지와 연관되서 유용하게 사용된다.

{% page-ref page="../backbonejs/url.md" %}

{% page-ref page="strongparameter.md" %}


