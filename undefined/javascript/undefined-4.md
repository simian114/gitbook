# 오디오 문제 이슈

{% embed url="https://stackoverflow.com/questions/30069988/how-can-i-create-a-promise-for-the-end-of-playing-sound" %}



{% embed url="https://developer.mozilla.org/en-US/docs/Web/API/HTMLAudioElement/Audio" %}



## 상황

A 라는 버튼이 여러 개 있다고 하자. 이 A 버튼을 누르면 눌린 버튼은 사라진다. 사라지면서 그 버튼에 해당하는 **audio 을 실행시키고 버튼이 제거되야한다.**

해당 버튼의 html 을 보면 아래처럼 되어있다.

```markup
<button class="A">
  <audio src="something.mp3"></audio>
</button>
```

```javascript
const button = document.querySelector('.A');
button.addEventListener('click', () => {
  const audio = button.querySelector('audio');
  audio.play();        // 오디오가 실행되지 않는다.
  button.remove();
})
```

**button A를 클릭하면 오디오가 나오지 않는다.**

## 해결

문제의 원인은 음악이 실행됨과 동시에 버튼삭제 -- 오디오 요소의 삭제로 이어지는 과정에서 오디오가 먹혔던 것. 해결 방법은 **프라미스 객체를 만드는 것**

```javascript
function play(url) {
  return new Promise(function (resolve, reject) {
    var audio = new Audio();
    audio.preload = "auto";
    audio.autoplay = true;
    audio.onerror = reject;
    audio.onended = resolve;

    audio.src = url;
  });
}
```

위 코드를 설명하자면, 앞으로 버튼을 누르면

1. 인자로 들어온 url 을 통해 **audio 태그를 생성한다.** 
2. **play 함수는 프라미스 객체를 리턴한다.**
3. 프라미스 객체의 **executor**  에서는
   1. **Web APIs** 에 구현되어 있는 `Audio` 를 이용해서 DOM 요소를 만든다.
   2. 자동실행
   3. 오디오 실행에 에러가 발생하면 **reject** 를 실행한다.
   4. 오디오가 실행되고 정상적으로 종료되면 **resolve** 를 실행한다.
      * 오디오는 종료되면 'ended' 이벤트를 발동한다!
   5. 오디오의 소스를 넣어준다.

이전에는 버튼 내부에 오디오 태그를 넣어줬지만 이제는 그럴필요 없이 버튼이 눌리면 오디오 태그를 만들고 실행하는 방법이 됐다. 위의 코드를 실행시키기 위해서는 아래처럼 하면된다.

```javascript
function pullUpCarrot(carrot) {
  const id = carrot.dataset.id;
  if (carrot.style.visibility == 'hidden') {
    return;
  }
  carrot.style.visibility = 'hidden';
  play('sound/carrot_pull.mp3')
    .then(() => {
      carrots.items[id] = 0;
      carrot.remove();
      if (--carrots.remain == 0) {
        score.innerText = carrots.remain;
        gameStop('win');
      } else {
        score.innerText = carrots.remain;
      }
    })
}
```

**play\(\).then** 처럼 프라미스의 문법을 사용하면 끝~

