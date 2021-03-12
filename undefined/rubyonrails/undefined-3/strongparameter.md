# StrongParameter 쿼리 배열 받기

```ruby
# controller...
def service_params
  params.permit(:id, :name, status: [])
end
```

* **permit** 에서 배열로 받을 속성을 `[ ]` 로 만들어 준다.
* **명심할 건, 배열로 받을 속성은 마지막에 있어야한다!!**

## 아래 두 페이지와 연관되서 유용하게 사용된다.

{% page-ref page="../../backbonejs/url.md" %}

{% page-ref page="../undefined-7/where.md" %}



