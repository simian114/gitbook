# arrow function 을 이용한 bind 이슈 해결

## 상황

```javascript
export default class Field {
  constructor(carrotCount, bugCount) {
        ...
    this.field.addEventListener('click', this.onClick);
  }
  onClick(event) {
    const target = event.target;
    if (target.matches('.carrot')) {
      target.remove();
      sound.playCarrot();
      this.onItemClick && this.onItemClick('carrot');
    } else if (target.matches('.bug')) {
      this.onItemClick && this.onItemClick('bug');
    }
  }
...
}
```

위으 코드에서는 클래스의 생성자에서 `addEventListener`을 해주고 있다. 그리고 이벤트 핸들러로 `onClick` 메서드가 실행된다.

보기에는 잘될거같지만... 문제가 생긴다. 바로 `this`!! 이 문제는 간단하게 해결할 수 있다. 하지만 기존의 방법보다 더 좋은 방법을 찾게되어서 적어본다.

## 해결

### 1. 기존에 사용하던 방법

기존에는 간단하게 **bind** 메서드를 통해서 해결했다.

```javascript
constructor(carrotCount, bugCount) {
    ...
  this.field.addEventListener('click', this.onClick.bind(this));
}
```

이렇게 되면 `onClick` 내부에서의 `this` 는 해당 객체가 되기 때문에 문제 해결!

### 2. 새로 알게된 방법

> arrow function 을 이용한다.

```javascript
constructor(carrotCount, bugCount) {
    ...
  this.field.addEventListener('click', event => this.onClick(event));
}
```

Arrow function 이 실행컨텍스를 건너뛴다는 건 알고있었지만, 막상 사용하지는 못하고 있었다. 새로운 방법을 알게 되서 기분이 좋다!

### arrow function 을 사용한 더 우아한 방법

```javascript
export default class Field {
  constructor(carrotCount, bugCount) {
        ..
    this.field.addEventListener('click', this.onClick);
  }
  // 메서드 자체를 arrow function 으로 만들자!
  onClick = event => {
    const target = event.target;
    if (target.matches('.carrot')) {
      target.remove();
      sound.playCarrot();
      this.onItemClick && this.onItemClick('carrot');
    } else if (target.matches('.bug')) {
      this.onItemClick && this.onItemClick('bug');
    }
  }
...
}
```

위의 방법은 **메서드 자체를 arrow function 으로 만든것!** 현업에서는 이렇게 자주 사용되는 것 같다.

