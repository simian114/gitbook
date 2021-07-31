# 목요일

{% page-ref page="../../../undefined/html-css/grid.md" %}

## 알게된것

* `CSS - Grid` 사용법
  * [그리드 정리](https://simian114.gitbook.io/blog/undefined/html-css/grid)
* `CRA` 에서의 절대경로 사용법
  1. `jsconfig.json`에 아래의 코드를 작성해준다.

     ```javascript
     {
       "compilerOptions": {
         "baseUrl": "src"
       },
       "include": [
         "src"
       ]
     }
     ```

  2. `package.json`을 수정해준다.

     ```javascript
     "scripts": {
       "start": "NODE_PATH=src react-scripts start",
       "build": "NODE_PATH=src react-scripts build",
     },
     ```

     `package.json`을 수정하는 것 말고도 `.env` 파일을 수정해서 하는 방법도 있다.
* 리액트 클래스 컴포넌트 사용법
  * 역시 [공식문서](https://ko.reactjs.org/docs/react-component.html) 가 최고다. 꼭 읽어보자.
  * 이전까지 `Hooks` 만 사용해서 처음에는 조금 헷갈리는게 많았다.
  * 클래스 컴포넌트는 훅과 다르게 클래스로 만들어진다. 그리고 클래스의 인스턴스로 만들어짐에 따라 **Hooks 와 다르게 진정한 의미의 생명 주기를 갖는다.**
    * 훅 - 함수형 컴포넌트는 클래스의 인스턴스와 다르게 함수 하나가 실행이 된다. 즉, 자신의 생명주기를 갖지 못한다. 이 문제를 해결하기 위해 리액트팀은 훅이라는 개념을 도입했고, 덕분에 함수형 컴포넌트에서도 생명주기를 흉내낼 수 있게 되었다.
    * 클래스로 관리하게 되면서 `this` 를 사용할 수 있다.
      * `this` 오랜만에 `class` 에서 `this` 를 사용해서 조금 헷갈렸다.
      * 개인적으는 `class` 안에서 인스턴스 메서드를 `arrow-function` 으로 만드는걸 좋아하지 않는다. 하지만 이번에는 팀원들이 다들 `arrow` 을 사용해서 나도 사용해봤다. 그런데 굳이 `arrow-function` 으로 한줄짜리 함수를 만들거면 `handler` 함수를 만들바에 컴포넌트에 바로 `inline-function` 으로 넣는게 더 좋지 않을까?
  * `class` 의 `render` 함수는 `함수형 컴포넌트`에서 `Hooks` 을 제외한 코드와 동일하다.
  * 
* `ref` 의 사용
  * 이번에 만든 모달은 `Deem` 을 누르면 모달이 종료가 된다.
  * 이 기능을 구현하기 위해서 `Ref` 를 사용했다. `Ref` 는 이해하기도 쉽고 사용하기도 정말 간편한거 같다. 앞으로 애용하게 될 거 같다.
    * 그렇다고 함부로 사용하지는 말자.
* `git`
  * 로컬에서 관리하는 `리모트 브랜치` 에 문제 생기는 경우가 있다. 이 때는 간단하게 로컬에서 관리하는 리모트 브랜치를 삭제해주자. 명령어는 `git branch -r -D [branch-name]`이다.

