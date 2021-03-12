# css 파일을 수정했는데 적용이 안된다?

{% hint style="info" %}
\*\*\*\*[**rails assets-pipeline**](https://guides.rubyonrails.org/asset_pipeline.html)\*\*\*\*
{% endhint %}

현재 css 파일은 `assets` 폴더에서 관리가 된다. 이 폴더에서 관리가 된다는 건 `webpack` 으로 컴파일하는게 아닌 `sprocket` 에서 관리한다는 것.

따라서 `css` 파일을 수정해도 페이지에 들어갈 때 **컴파일**하지 않는 경우가 부지기수다.

이럴 때는 아래 명령어로 `assets` 을 컴파일 시킨다.

`rails assets:precompile`

