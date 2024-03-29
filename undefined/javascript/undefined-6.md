# 브라우저 동작 원리

## 브라우저의 동작 원리

### 브라우저의 주요 기능

* 브라우저의 주요 기능은 사용자가 **원하는** **웹페이지를 서버에 요청\(Request\)하고 서버의 응답\(Reponse\)을 받아 브라우저에 표시하는 것이다.**
* 브라우저는 HTML, CSS 명세에 따라 **HTML파일을 해석해서 보여준다.**
* 이 명세는 웹 표준화 기구인 W3C\(World Wide Web Consortium\)에서 정한다.

### 브라우저의 기본구조

1. **유저 인터페이스** : 이전, 다음, 새로고침, 북마크 같이 요청한 페이지는 보여주는 부분 외에 유저에게 동일하게 보이는 부분이다.
2. **브라우저 엔진** : 유저 인터페이스와 렌더링 엔진 사이에 쿼리를 전달할 수 있게 조작을 담당합니다.
3. **렌더링 엔진** : 사용가자가 요청한 페이지는 화면에 표시하기 위해 HTML과 CSS를 파싱/해석합니다.
4. **네트워킹** : http 요청을 할 수 있고, 네트워크 호출에 사용됩니다.
5. **자바스크립트 해석기** : Javascript코드를 해석 및 실행합니다.
6. **UI 벡엔드** : 기본적인 UI들 예로 input, select 같은 위젯을 그리는 인터페이스입니다.
7. **자료 저장소** : 쿠키같은 지원들을 저장하는 영역이다.

### 브라우저의 기본 구조

![&#xBE0C;&#xB77C;&#xC6B0;&#xC800;&#xC758; &#xAE30;&#xBCF8; &#xAD6C;&#xC870;](https://d2.naver.com/content/images/2015/06/helloworld-59361-1.png)

### 렌더링 엔진

렌더링 엔진의 역할은 요청 받은 내용을 브라우저 화면에 표시하는 일이다.

렌더링 엔진은 HTML 및 XML 문서와 이미지를 표시할 수 있다. 물론 플러그인이나 브라우저 확장 기능을 이용해 PDF와 같은 다른 유형도 표시할 수 있다. 그러나 이 장에서는 HTML과 이미지를 CSS로 표시하는 주된 사용 패턴에 초점을 맞출 것이다.

#### 렌더링 엔진들

이 글에서 다루는 브라우저인 파이어폭스와 크롬, 사파리는 두 종류의 렌더링 엔진으로 제작되었다. 파이어폭스는 모질라에서 직접 만든 게코\(Gecko\) 엔진을 사용하고 사파리와 크롬은 웹킷\(Webkit\) 엔진을 사용한다.

웹킷은 최초 리눅스 플랫폼에서 동작하기 위해 제작된 오픈소스 엔진인데 애플이 맥과 윈도우즈에서 사파리 브라우저를 지원하기 위해 수정을 가했다. 더 자세한 내용은 [webkit](http://webkit.org/)[.](http://webkit.org/)[org](http://webkit.org/)를 참조한다.

#### 동작 과정

렌더링 엔진은 통신으로부터 요청한 문서의 내용을 얻는 것으로 시작하는데 문서의 내용은 보통 8KB 단위로 전송된다.

다음은 렌더링 엔진의 기본적인 동작 과정이다.

![brouser2](https://d2.naver.com/content/images/2015/06/helloworld-59361-2.png)

그림 2 렌더링 엔진의 동작 과정

렌더링 엔진은 HTML 문서를 파싱하고 "콘텐츠 트리" 내부에서 태그를 [DOM](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/) 노드로 변환한다. 그 다음 외부 CSS 파일과 함께 포함된 스타일 요소도 파싱한다. 스타일 정보와 HTML 표시 규칙은 "[렌더](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)[ ](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)[트리](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)"라고 부르는 또 다른 트리를 생성한다.

렌더 트리는 색상 또는 면적과 같은 시각적 속성이 있는 사각형을 포함하고 있는데 정해진 순서대로 화면에 표시된다.

렌더 트리 생성이 끝나면 배치가 시작되는데 이것은 각 노드가 화면의 정확한 위치에 표시되는 것을 의미한다. 다음은 UI 백엔드에서 렌더 트리의 각 노드를 가로지르며 형상을 만들어 내는 그리기 과정이다.

일련의 과정들이 점진적으로 진행된다는 것을 아는 것이 중요하다. 렌더링 엔진은 좀 더 나은 사용자 경험을 위해 가능하면 빠르게 내용을 표시하는데 모든 HTML을 파싱할 때까지 기다리지 않고 배치와 그리기 과정을 시작한다. 네트워크로부터 나머지 내용이 전송되기를 기다리는 동시에 받은 내용의 일부를 먼저 화면에 표시하는 것이다.

#### 동작 과정 예

![brouser3](https://d2.naver.com/content/images/2015/06/helloworld-59361-3.png)

그림 3 웹킷 동작 과정

![brouser4](https://d2.naver.com/content/images/2015/06/helloworld-59361-4.png)

그림 4 모질라의 게코 렌더링 엔진 동작 과정\(3.6\)

웹킷과 게코가 용어를 약간 다르게 사용하고 있지만 동작 과정은 기본적으로 동일하다는 것을 그림 3과 그림 4에서 알 수 있다.

게코는 시각적으로 처리되는 렌더 트리를 "형상 트리\(frame tree\)"라고 부르고 각 요소를 형상\(frame\)이라고 하는데 웹킷은 "렌더 객체\(render object\)"로 구성되어 있는 "렌더 트리\(render tree\)"라는 용어를 사용한다. 웹킷은 요소를 배치하는데 "배치\(layout\)" 라는 용어를 사용하지만 게코는 "리플로\(reflow\)" 라고 부른다. "어태치먼트\(attachment\)"는 웹킷이 렌더 트리를 생성하기 위해 DOM 노드와 시각 정보를 연결하는 과정이다. 게코는 HTML과 DOM 트리 사이에 "콘텐츠 싱크\(content sink\)"라고 부르는 과정을 두는데 이는 DOM 요소를 생성하는 공정으로 웹킷과 비교하여 의미있는 차이점이라고 보지는 않는다.

