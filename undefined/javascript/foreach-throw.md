# forEach는 반복도중 멈출 방법이 throw 밖에 없다!

{% embed url="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global\_Objects/Array/forEach" caption="MDN forEach" %}

```javascript
const a = [1,2,3,4,5]
a.forEach((n) => {
    if (n == 2) {
        return 
    }
    console.log(n);
})
```

와 같은 코드가 있을 때 **일반적인 배열의 반복이었다면 n == 2 였을 때 바로 반복이 종료되었을 것이다.** 하지만 지금은 **forEach 를 통한 배열의 반복이다. forEach 배열의 반복에서 중간에 멈추는 방법은 throw 가 던져지는 것 단 하나뿐이다.**

공식 사이트에 이런 문구가 있다.

> 예외를 던지지 않고는 `forEach()`를 중간에 멈출 수 없습니다. 중간에 멈춰야 한다면 `forEach()`가 적절한 방법이 아닐지도 모릅니다.
>
> 다음 방법으로는 조기에 반복을 종료할 수 있습니다.
>
> * 간단한 [for](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for) 반복문
> * [for...of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of), [for...in](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...in) 반복문
> * [`Array.prototype.every()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/every)
> * [`Array.prototype.some()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
> * [`Array.prototype.find()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
> * [`Array.prototype.findIndex()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)
>
> 다른 배열 메서드 [`every()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/every), [`some()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/some), [`find()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/find), [`findIndex()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)는 배열 요소를 판별 함수에 전달하고, 그 결과의 참/거짓 여부에 따라 반복의 종료 여부를 결정합니다.

