# Drag & Drop 를 이용해서 리스트 요소 순서 바꾸기

> [MDN - HTML 드래그 앤 드롭 API](https://developer.mozilla.org/ko/docs/Web/API/HTML_Drag_and_Drop_API)
>
> [MDN - Drag Operations\(실습\)](https://developer.mozilla.org/ko/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations)
>
> [HTML SPEC - Drag and drop](https://html.spec.whatwg.org/multipage/dnd.html#dndevents)
>
> [Codepan example](https://codepen.io/retrofuturistic/pen/tlbHE)
>
> [How to Implement Drag and Drop Feature for Your React Component](https://www.pluralsight.com/guides/implement-drag-drop-react-component)

`todo list` 를 구현했는데, 뭔가 너무 아쉬웠다. 평소에 구현해보고 싶었던 기술중에 `Drag & Drop` 이 있었는데 `todo list` 에 적용하기 너무 좋아보여서 시도했다.

## 인터페이스

DnD 의 인터페이스로는 크게 4가지가 존재한다.

* [`DragEvent`](https://developer.mozilla.org/ko/docs/Web/API/DragEvent)
* [`DataTransfer`](https://developer.mozilla.org/ko/docs/Web/API/DataTransfer)
* [`DataTransferItem` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/DataTransferItem)
* [`DataTransferItemList` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/DataTransferItemList) 

`DateEvet` 와 `DataTranfer` 만 알아보자.

### DragEvent

| 이벤트 | 이벤트 핸들러 | 설명 |
| :--- | :--- | :--- |
| `dragstart (en-US)` | [`ondragstart` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragstart) | 사용자가 요소나 텍스트 블록을 드래그하기 시작했을 때 발생한다. \([드래그 시작하기](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations#dragstart)를 보시오.\) |
| [`drag`](https://developer.mozilla.org/ko/docs/Web/API/Document/drag_event) | [`ondrag` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondrag) | 요소나 텍스트 블록을 드래그 할 때 발생한다. |
| `dragend (en-US)` | [`ondragend` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragend) | 드래그를 끝냈을 때 발생한다. \(마우스 버튼을 떼거나 ESC 키를 누를 때\) \([드래그 끝내기](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations#dragend)를 보시오\) |
| `dragenter (en-US)` | [`ondragenter` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragenter) | 드래그한 요소나 텍스트 블록을 적합한 드롭 대상위에 올라갔을 때 발생한다. \([드롭 대상 지정하기](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations#droptargets)를 보시오.\) |
| `dragover (en-US)` | [`ondragover` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragover) | 요소나 텍스트 블록을 적합한 드롭 대상 위로 지나갈 때 발생한다. \(매 수백 밀리초마다 발생한다.\) |
| `dragleave (en-US)` | [`ondragleave` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragleave) | 드래그하는 요소나 텍스트 블록이 적합한 드롭 대상에서 벗어났을 때 발생한다. |
| `drop (en-US)` | [`ondrop` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondrop) | 요소나 텍스트 블록을 적합한 드롭 대상에 드롭했을 때 발생한다. \([드롭하기](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations#dragstart)를 보시오.\) |
| `dragexit` | [`ondragexit` \(en-US\)](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/ondragleave) | 요소가 더 이상 드래그의 직접적인 대상이 아닐 때 발생한다. |

> Drag 관련 이벤트. 여기서 중요한건 이벤트의 발동 주체가 2가지로 나뉜다는것임.

#### 1. 드래그 요소

> 마우스로 클릭하고 끌고 다니는 요소

* drag
* dragStart
* dragEnd

#### 2. 드래그 요소와 겹치는 아이템

> [Define a drop zone](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API#define_a_drop_zone)

* dragEnter
* dragOver \(필수로 구현\)
* dragLeave
* drop \(필수로 구현\)

여기서 주목할 건 `Drag` 요소를 다른 요소 어딘가에 놓으면 그에 대한 이벤트는 `drop` 이 발생하고 이 `drop` 을 핸들링 하는 곳이 `Drag` 된 요소가 아니라 **그 요소와 겹치는 아이템이라는 것.**

여기서 의문이 생긴다. `Drag` 된 요소가 아니라 그 요소가 떨어진 곳에서 `Drop` 이벤트 핸들러를 실행한다면, `Drag` 된 요소의 정보는 어떻게 넘겨주는거지? 그 의문은 `DataFransfer` 에서 해결된다.

### DataTransfer

> [https://developer.mozilla.org/ko/docs/Web/API/DataTransfer](https://developer.mozilla.org/ko/docs/Web/API/DataTransfer)
>
> `get`, `set` 이외에도 여러 메서드, 프로퍼티가 있다. 필요하면 위의 링크로 가서 보자.

실제로 드래그를 이용해서 리스트 요소의 순서를 바꾸는걸 생각해보자.

1. 요소를 마우스로 잡는다.
   * 마우스로 잡은 요소에서 `dragStart` 이벤트를 핸들링한다.
2. 끌고 다닌다.
3. 원하는 곳에 내려놓는다.
   * 내려놓은 곳의 요소에서 `drop` 이벤트가 발동된다.

우리는 **1번에서 요소에 대한 정보를 어딘가에 저장해야하고, 그 정보를 3번에서 이용해야한다.** 이걸 해주는게 `DataTransfer`다. 사용법은 매우 간단하다. `setData` 를 통해 데이터를 저장하고, `getData` 를 이용해서 저장된 정보를 불러온다.

> [여기를 참고하자](https://developer.mozilla.org/ko/docs/Web/API/DataTransfer#methods)

실제 사용은 아래처럼 하면된다.

```javascript
...
// 1번. drag 요소의 데이터를 저장
const handleDragStart = (e) => {
  // key는 getData 에서 사용할 key
  // value 는 저장할 데이터
  e.dataTransfer.setData([key], [value]);
}

// 3번. 놓임 당한 곳에서 drag 된 요소 가져오기
const handleDrop = (e) => {
  e.dataTransfer.getData([key]);
  .. 추가 작업들...
}
...
```

`localStorage` 처럼 `get`, `set` 으로 사용하면 된다.

## 실제 사용하기

> [todoList 내의 todo 들을 드래그해서 옮기는 코드를 예로 든다. 코드는 여기를 눌러서 확인하자.](https://github.com/simian114/wanted-preOnBoarding-project8)
>
> 아래의 코드는 미래 재사용을 위해 `useDragAndDrop` 이라는 커스텀훅으로 만들었다.

### 1. draggable 속성 넣기

> 지금까지 내가 만든 웹요소들은 드래그가 모두 불가능했었다. 왜? 바로 `draggable` 속성을 넣지 않았기 때문이다. 즉 `draggable` 속성을 넣으면 요소들은 드래깅이 가능해진다.

```javascript
...
return {
  <TodoItemContainer
      draggable
         ...
    >
}
```

`draggable` 속성을 넣음으로써 API 를 사용할 수 있게 된다.

### 2. onDragStart 핸들러

드래그의 시작을 알려준다. 인터페이스 부분에서 설명했다시피 이 부분에서 옮기는 요소에 대한 정보를 저장해야한다.

```javascript
const handleDragStart = (e: React.DragEvent<HTMLDivElement>) => {
  setisDragging(true); // 1
  e.dataTransfer.effectAllowed = 'move'; // 2
  e.dataTransfer.setData('text/plain', id); // 3
};
```

코드를 분석해보자면

1. `setIsDragging`
   * 현재 `todo` 가 드래그 하는 중이라는걸 알려주는 `state` 다.
   * 이 값을 통해 `todo` 는 자신이 드래깅중인지 또는 아닌지를 알 수 있다.
     * `css` 핸들링에 사용할 수 있다.
2. `e.dataTransfer.effectedAllowed = move`
   * 드래그를 해보면 디폴트로 마우스 아래에 `+` 버튼이 생긴다. 이 버튼을 막아준다. [https://developer.mozilla.org/en-US/docs/Web/API/HTML\_Drag\_and\_Drop\_API/Drag\_operations\#drageffects](https://developer.mozilla.org/en-US/docs/Web/API/HTML_Drag_and_Drop_API/Drag_operations#drageffects).
3. `e.dataTransfer.setData('text/plain', id);`
   * **드래그 하는 요소의 정보를 담는다.**

### 3. onDragOver 핸들러

> Dropzone

tkdghk

```javascript
const handleDragOver = (e: React.DragEvent<HTMLDivElement>) => {
  e.preventDefault(); // 1
  setIsDragOver(true); // 2
};
```

1. `e.preventDefault();`
   * 기본으로 발동하는 다른 이벤트들을 막는다. \(Touch, pointer events..\)
2. `setIsDragOver(true);`
   * 현재 요소가 드래그 요소의 아래 깔려있음을 말해준다.

### 4. onDrop 핸들러

> Dropzone

```javascript
const handleDrop = (e: React.DragEvent<HTMLDivElement>) => {
  e.preventDefault();
  setisDragging(false);
  setIsDragOver(false);
  const movingTarget = e.dataTransfer.getData('text/plain'); // 1
  callback(movingTarget, id); // 2
};
```

1. `const movingTarget = e.dataTransfer.getData('text/plain');`
   * 이전에 저장한 데이터를 가져온다.
2. `callback()`
   * `todo` 요소의 위치를 바꾸는 함수다.

