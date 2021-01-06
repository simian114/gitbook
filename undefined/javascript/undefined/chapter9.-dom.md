# Chapter9. DOM

## DOM

* _**이상한 TCP를 보지 말고**_ [_**여기**_](https://wit.nts-corp.com/2019/02/14/5522)_**를 보자**_
* [모던 자바스크립트 듀토리얼 DOM](https://ko.javascript.info/dom-nodes)
* \[MDN 공식 문서\]\([https://developer.mozilla.org/ko/docs/Web/API/Document\_Object\_Model/%EC%86%8C%EA%B0%9C](https://developer.mozilla.org/ko/docs/Web/API/Document_Object_Model/소개)\)
* 
### DOM

* DOM 이란 문서 객체 모델\(Document Object Model\)의 약자다.
* DOM은 XML이나 HTML 문서에 접근하기 위한 일종의 인터페이스다.
* 이 객체 모델은 문서 내의 모든 요소를 정의하고, 각각의 요소에 접근하는 방법을 제공한다.
* DOM은 W3C의 표준 객체 모델이고 계층 구조료 표현이 된다.
* 모든 HTML 태그는 객체다
* 태그 하나가 감싸고 있는 `자식` 태그는 중첩 태그라고 부른다.
  * 태그 내의 문자\(text\) 역시 객체다.

## Document 객체

### Document 객체

* Document 객체는 웹 페이지 그 자체를 의미한다
* 웹 페이지에 존재하는 HTML 요소에 접근하고자 할 때는 반드시 Document 객체부터 시작해야 한다.

### Document 메서드

1. HTML 요소의 선택
2. HTML 요소의 생성
3. HTML 이벤트 핸들러 추가
4. HTML 객체의 선택

### HTML 요소의 선택

* | 메소드 | 설명 |
  | :---: | :---: |
  | document.getElementsByTagName\(태그이름\) | 해당 태그 이름의 요소를 모두 선택함. |
  | document.getElementById\(아이디\) | 해당 아이디의 요소를 선택함. |
  | document.getElementsByClassName\(클래스이름\) | 해당 클래스에 속한 요소를 모두 선택함. |
  | document.getElementByName\(name속성값\) | 해당 name 속성값을 가지는 요소를 모두 선택함. |
  | document.querySelectorAll\(선택자\) | 해당 선택자로 선택되는 요소를 모두 선택함. |

### HTML 요소의 생성

* | 메소드 | 설명 |
  | :---: | :---: |
  | document.createElement\(HTML요소\) | 지정된 HTML 요소를 생성함. |
  | document.write\(텍스트\) | HTML 출력 스트림을 통해 텍스트를 출력함. |

### HTML 이벤트 핸들러 추가

* | 메소드 | 설명 |
  | :---: | :---: |
  | document.getElementById\(아이디\).onclick = function\(\){ 실행할 코드 } | 마우스 클릭 이벤트와 연결될 이벤트 핸들러 코드를 추가함. |

### HTML 객체의 선택

* | 객체 집합 | 설명 | DOM Level |
  | :---: | :---: | :---: |
  | document.anchors | name 속성을 가지는 요소를 모두 반환함. | 1 |
  | document.applets | applet 요소를 모두 반환함. \(HTML5에서 제외됨\) | 1 |
  | document.body | 요소를 반환함. | 1 |
  | document.cookie | HTML 문서의 쿠키\(cookie\)를 반환함. | 1 |
  | document.domain | HTML 문서가 위치한 서버의 도메인 네임\(domain name\)을 반환함. | 1 |
  | document.forms | 요소를 모두 반환함. | 1 |
  | document.images | 요소를 모두 반환함. | 1 |
  | document.links | href 속성을 가지는 요소와 요소를 모두 반환함. | 1 |
  | document.referrer | 링크\(linking\)되어 있는 문서의 URI를 반환함. | 1 |
  | document.title | 요소를 반환함. | 1 |
  | document.URL | HTML 문서의 완전한 URL 주소를 반환함. | 1 |
  | document.baseURI | HTML 문서의 절대 URI\(absolute base URI\)를 반환함. | 3 |
  | document.doctype | HTML 문서의 문서 타입\(doctype\)을 반환함. | 3 |
  | document.documentElement | 요소를 반환함. | 3 |
  | document.documentMode | 웹 브라우저가 사용하고 있는 모드를 반환함. | 3 |
  | document.documentURI | HTML 문서의 URI를 반환함. | 3 |
  | document.domConfig | HTML DOM 설정을 반환함. \(더는 사용하지 않음\) | 3 |
  | document.embeds | 요소를 모두 반환함. | 3 |
  | document.head | 요소를 반환함. | 3 |
  | document.implementation | HTML DOM 구현\(implementation\)을 반환함. | 3 |
  | document.inputEncoding | HTML 문서의 문자 인코딩\(character set\) 형식을 반환함. | 3 |
  | document.lastModified | HTML 문서의 마지막 갱신 날짜 및 시간을 반환함 | 3 |
  | document.readyState | HTML 문서의 로딩 상태\(loading status\)를 반환함. | 3 |
  | document.scripts | 요소를 모두 반환함. | 3 |
  | document.strictErrorChecking | 오류의 강제 검사 여부를 반환함. | 3 |

## DOM 요소

### DOM 요소의 선택

* HTML 요소를 다루기 위해서는 우선 해당 요소를 _**선택**_ 해야만 한다.
  1. HTML 태그 이름을 이용한  선택
  2. `id 속성` 을 이용한 선택
  3. `class 속성` 을 이용한 선택
  4. `name 속성`을 이용한 선택
  5. `CSS 선택자` 를 이용한 선택
  6. `HTML 객체 집합` 을 이용한 선택

### HTML 태그 이름을 이용한 선택

* `getElementsByTagName()` 메서드
  * ```javascript
    var selected = document.getElementsByTagName('a')
    for (var i = 0; i < selected.length; i++)
      selected.item(i).style.color = 'red';
    ```
  * 태그 이름에 해당하는 모든 객체를 선택하는거니까 리턴값은 배열...?
  * `item` 메서드로 구해진 각 요소에 접근할 수 있는듯.

### ID를 이용한 선택

* `getElementById()`
  * ID는 유일한 값이므로 하나만을 선택한다

### 클래스를 이용한 선택

* `getElementsByClassName()`
  * tag name과 비슷하게 여러개를 한번에 선택한다

### name 속성을 이용한 선택

* `getElementsByName()`
  * 여러개를 한번에 선택

### CSS 선택자를 이용한 선택

* `querySelectorAll()`

### HTML 객체 집합을 이용한 선택

* HTML DOM에서 제공하는 객체 집합을 이용하여 HTML 요소를 선택
* ```javascript
  var test = document.body; // <body> 요소를 선택함
  ```

## 노드

### 노드란

* HTML DOM 은 노드 라고 불리는 계층적 단위에 정보를 저장한다.
* DOM 은 이러한 노드들을 정의하고 그들 사이의 관계를 설명해 주는 역할을 한다.
* `HTML` 문서의 정보는 노드 트리 라고 불리는 계층적 구조에 저장된다.
* 노드 트리는 노드들의 집합이며, 노드 간의 관계를 보여준다.
* ![&#xB178;&#xB4DC; &#xD2B8;&#xB9AC;](http://www.tcpschool.com/lectures/img_js_htmldom.png)

### 노드의 종류

> HTML 문서의 모든 것은 노드다

* 대표적인 노드는 아래와 같다.
* | 노드 | 설명 |
  | :---: | :---: |
  | 문서 노드\(document node\) | HTML 문서 전체를 나타내는 노드임. |
  | 요소 노드\(element node\) | 모든 HTML 요소는 요소 노드이며, 속성 노드를 가질 수 있는 유일한 노드임. |
  | 속성 노드\(attribute node\) | 모든 HTML 요소의 속성은 속성 노드이며, 요소 노드에 관한 정보를 가지고 있음.하지만 해당 요소 노드의 자식 노드\(child node\)에는 포함되지 않음. |
  | 텍스트 노드\(text node\) | HTML 문서의 모든 텍스트는 텍스트 노드임. |
  | 주석 노드\(comment node\) | HTML 문서의 모든 주석은 주석 노드임. |

### 노드간의 관계

* 노드 트리의 가장 상위에는 _**단 하나의 루트 노드가 존재한다.**_
* 루트 노드를 제외한 모든 노드는 _**단 하나의 부모 노드만을 가진다.**_
* 모든 요소 노드는 자식 노드를 가질 수 있다.

## 노드로의 접근

1. `getElementsByTagName()` 메서드를 이용하는 방법
2. 노드 간의 관계를 이용하여 접근하는 방법

### getElementsByTagName\(\)

* 특정 태그 이름을 가지는 모든 요소를 `노드 리스트`의 형태로 반환한다.

### 노드 간의 관계를 이용하여 접근하는 방법\(메서드\)

1. parentNode
2. childNodes
3. firstChild
4. lastChild
5. nextSibling
6. previousSibling

### 노드에 대한 정보\(메서드\)

1. nodeName
2. nodeValue
3. nodeType

## 노드 리스트

### 노드리스트\(node list\)

* 노드 리스트는 `getElementsByTagName()` 메서드나 `childNodes` 프로퍼티의 값으로 반환되는 객체다.
  * `getElementById` 처럼 하나가 아닌 여러개를 받을 때 리턴되는 객체
* 배열처럼 `[]`로 접근하거나 `item` 메서드로 접근하면 된다.
* ```javascript
  var lis = document.getElementsByTagName('li')
  var first_li = list.item(0)
  ```

## 노드의 관리

### 노드의 추가

1. `appendChild()`
2. `insertBefore()`
3. `insertData()`

### appendChild\(\) 메서드

* 새로운 노드를 해당 노드의 _**자식 노드 리스트의 맨 마지막에 추가한다.**_
* ```javascript
  body = document.body;
  a = document.createElement('a'); // <a> </a>
  text = document.createTextNode('This is link');
  attr = document.createAttribute('href')
  attr.value = 'www.naver.com'

  a.appendChild(text);
  a.setAttributeNode(attr);

  body.appendChild(a)
  ```

### insertBefore\(\) 메서드

* 새로운 노드를 _**특정 자식 바로 앞에 추가한다.**_
* ```text
  parentNode.insertBefore(새로운 노드, 기준 자식 노드)
  ```

### insertData\(\) 메서드

* 텍스트 노드의 텍스트 데이터에 새로운 텍스트를 _**추가**_한다.
* ```javascript
  text_node.insertData(offset, new_data);

  var a = document.getElementsByTagName('a')[0].childNodes[0];
  a.insertData(0, "haha! "); // offset이 기존 길이를 넘어서면 예외발생
  ```

### 노드의 생성

1. createElement\(\)
2. createAttribute\(\)
3. createTextNode\(\)

```javascript
body = document.body;
a = document.createElement('a'); // <a> </a>
text = document.createTextNode('This is link');
attr = document.createAttribute('href')
attr.value = 'www.naver.com'

a.appendChild(text);
a.setAttributeNode(attr);

body.appendChild(a)
```

### 노드의 복제 - cloneNode\(\) 메서드

* ```javascript
  대상.cloneNode(자식노드 복제 여부)
  ```

## 노드의 조작

### 노드의 값 변경

* `nodeValue` 프로퍼티를 사용하면 특정 노드의 값을 변경할 수 있다.
* `setAttribute` 메서드는 속성 노드의 속성값을 변경할 수 있게 해준다

### 요소 노드의 텍스트

* 요소 노드는 자신이 직접 텍스트값을 가지지 않는다.
* 자식 노드인 텍스트 노드에 저장이 된다.
  * 따라서 텍스트 값을 변경하려면 텍스트 노드에 접근해야한다.

### 속성 노드의 값 변경

* 속성 노드는 `nodeValude` 프로퍼티 뿐만 아니라 `setAttribute() 메서드`를 사용하여 값을 변경할 수 있다.
* 속성이 존재하지 않으면, 먼저 해당 속성을 생성한 후에 값을 설정한다.

### 요소 노드의 교체

* `replaceChild` 메서드를 사용하여 교체할 수 있다.
* ```javascript
  교체할노드 = 부모노드.replaceChild(새로운 자식 노드, 기존 자식)
  ```

