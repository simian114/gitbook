# Chatper1. 기본

## 자바스크립트 출력

1. `window.alert()`
2. HTML DOM 요소를 이용한 `innerHTML` 프로퍼티
3. `documnet.write()` 메서드
4. `console.log()` 메서드

### 1. window.alert\(\)

* 가장 간단함.
* ```javascript
  <script>
      function alertDialogBox() {
          alert("확인을 누를 때까지 다른 작업을 할 수 없어요!");
      }
  </script>
  ```
* 인자로 들어간 문자열이 알림창과 함께 나온다.

### 2. HTML DOM 요소를 활용한 innerHTML 프로퍼티

* 실제로 가장 많이 사용됨
* 먼저 document 객체의 `getElementByDI()` 나 `getElementsByTagName()` 등의 메서드를 사용하여 `HTML` 요소를 선택한다.
* 이후 `innerHTML` 프로퍼티를 이용하면 선택된 `HTML`요소의 내용이나 속성값등을 손쉽게 변경할 수 있다.
* ```javascript
      <h1>HTML DOM 요소를 이용한 innerHTML 프로퍼티</h1>
      <p id="text">이 문장을 바꿀 것입니다!</p>

      <script>
          var str = document.getElementById("text");
          str.innerHTML = "이 문장으로 바뀌었습니다!";
      </script>
  ```

### 3. Document.write\(\) 메서드

* 웹 페이지가 로딩될 떄 실행되면, 가장 먼저 데이터를 출력한다.
* 따라서 테스트나 디버깅을 위해 사용된다.
  * ```javascript
    <script>
        document.write(4 * 5);
    </script>
    ```

### 4. console.log\(\) 메서드

* 웹 브라우저의 콘솔을 통해 데이터를 출력한다.

## 자바스크립트를 적용하는 방법

1. 내부 자바스크립트 코드로 적용
2. 외부 자바스크립트 파일로 적용

### 1. 내부 코드로 적용

* `script` 태그를 사용하여 문서안에 삽입

```javascript
<script>
    document.getElementById("text").innerHTML = "여러분을 환영합니다!";
</script>
```

* Html 문서의 `head`, `body` 양쪽 모두에 위치할 수 있다.
* ```javascript
  <head>
      <meta charset="UTF-8">
      <title>JavaScript Apply</title>
      <script>
          function printDate() {
              document.getElementById("date").innerHTML = Date();
          }
      </script>
  </head>
  <body>
      <h1>head 태그 내의 자바스크립트</h1>
      <p>자바스크립트를 이용하면 현재 날짜와 시간 정보에도 손쉽게 접근할 수 있어요!</p>
      <button onclick="printDate()">현재 날짜와 시간 표시하기!</button>
      <p id="date"></p>
  </body>
  ```

### 2. 외부 자바스크립트 파일

* `.js` 확장자를 사용해 저장
* 적용하고 싶은 웹 페이지의 헤더에서 추가하자.
* ```javascript
  <head>
      <meta charset="UTF-8">
      <title>JavaScript Apply</title>
      <script src="/examples/media/example.js"></script>
  </head>
  ```

