# 백본 VIEW의 remove와 jquery의 remove 는 다르다!

## 문제

현재 view 에서 remove를 제이쿼리 remove를 해주는게 종종있다!

`view` 파일들을 보면 뷰 자체를 닫아 줄 때 우리는 `close` 함수를 이용해서 닫게 만들고 있다.

그런데 이 각 뷰 마다 이 함수를 사용하는게 조금은 다르다.

```javascript
// 1. jquery문법으로 DOM 삭제
this.$el.remove()

// 2. BackboneJS VIEW의 remove
this.remove()
```

우선 어떻게 해야하는지 정답을 말해보자면, _**뷰 객체 자체를 삭제하는 2 번을 사용해야 한다.**_

### jQuery의 remove

[여기를 보자](http://www.devkuma.com/books/pages/385) 를 보면 **jQuery** 의 `remove`를 확인할 수 있다. `remove` 는 DOM 요소 와 연관된 이벤트를 삭제한다.

DOM요소와 관련 이벤트도 삭제한다... 언뜻 보기에는 괜찮다. 하지만 실제로는 괜찮지 않다. 왜냐하면 _**백본의 뷰 에서는 이벤트가 DOM 요소 자체가 아니라, 모델과 컬렉션에 걸리기 때문!!**_

### BackboneJS의 remove

백본의 remove를 확인하려면 [공식 사이트 코드](https://backbonejs.org/docs/backbone.html) 에 들어가서 `remove: function()` 두 번째를 보면된다.

_**코드를 보면 제이쿼리의 remove 를 사용하고 더해서 listenTo 자체도 모두 stopListening 하는 동작까지 수행하는걸 볼 수 있다!**_

언뜻 보기에는 별거 아닌거 같아보이지만, 이벤트 자체 때문에 서비스가 망가질 수 있다는걸 명심하고 이 둘을 잘 분간해서 사용하자

