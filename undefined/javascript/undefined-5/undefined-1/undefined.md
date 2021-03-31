# 자바스크립트 기본 문법



async 와 defer

head 안에 스크립트 태그를 이용해 js 파일을 넣으면, 왜인지는 몰라도 웹페이지에서 JS 를 사용할 수가 없다.

따라서 바디가 다 준비된 다음에 js 를 다운받을 수 있도록 body의 마지막에 script 를 넣는 경우가 많음 근데 ㅣㅇ거도 단점이 있음.

사용자가 기본적인 html을 빨리 볼 수는 있지만 JS 요소가 많은 페이지라면 사용자가 JS 를 다운받기 전까지는 망가져있는 페이지를 볼수밖에 없음

head 안에 script 를 넣으면서 async 옵션을 넣는것. 이렇게 되면 병렬로 js를 다운받으면서 body 를 계속 파싱한다. js 다운이 끝나면 js 를 실행시키는 동안 파싱작업은 잠깐 멈추고 실행 준비가 끝나면 다시 html 을 파싱한다. 이것도 문제가 있는데, 바디 끝에 사용하는 것보다는 병렬적으로 일어나기 때문에 시간은 절약할 수 있지만, html 이 다 파싱되기전에 JS 가 다운로드 및 실행이 되어서 페이지내에서 어떤 요소를 찾아야하는데 그 요소가 아직 파싱이 안되서 에러가 발생할 수 있음.

defer 옵션: head 안에 스크립트를 넣고 defer 옵션을 넣으면 html 을 파싱하면서 js를 발견하면 다운로드 받자 명령만 시켜넣고 나머지 html을 끝까지 파싱한다. 그리고 파싱이 끝나면 그제서야 병렬로 다운로드 받아진 js 를 실행시킨다 이 방법이 가장 좋다!

async로 다수의 js 파일을 포함시키면 정의된 순서에 상관없이 다운로드 된 순서로 실행이 되기 때문에 동기에 있어서 문제가 생긴다.

반대로 defer 을 사용하면 html 파싱이 모두 끝난 후 선언 된 순서로 실행이 되기 때문에 예상 가능하다. 안전하다.

자바스크립트 클래스 알아보기

자바스크립트 배열 제대로 사용하기

* map
* reduce
* some
* every
* find
* 등등 멋진 함수가 있다.
* vscode 에서 함수 구현까지 이동할 수 있으니깐 구현 되어있는 모습도 살펴보자 

자바스크립트의 제이슨

```javascript
JSON.stringfy // obj 의 스틍링화
JSON.parse        //json 을 obj

const rabbit = {
  name: 'tori',
  color: 'white',
  size: 3,
  birthDate: new Date(),
  jump: () => {
    console.log(`${name} can jump!`);
  },
}

json = JSON.stringify(rabbit)

console.clear();
const obj = JSON.parse(json, (key, value) => {
  if (key == "birthDate")
    value = 
  console.log(`key: ${key}, value: ${value}`);
  return key === "birthDate" ? Date(value) : value;
});
console.log(json);
console.log(obj);
```

위처럼 Date 데이터를 JSON.stringfy 했다가 parse 하면 문자열형태로 되어있다. 이건 우리가 원하는 형태가 아니므로 타입을 바꿔주도록하자. 어떻게? 콜백함수로 ^\_^

## 프로미스

프로미스 객체는 new 로 생성되자마자 인자로 건네진 executor 함수가 바로 실행된다. 따라서 원치않는 실행으로 부하가 많이 걸릴 수 있으니 주의하자.

인자로 건네주는 resolve와 reject 는 겉보기로만 있는건가?

executor 의 성공 실패 여부에 따라 resolve, reject 함수를 따로 실행시켜주면 된다.

resolve 가 됐을 때는 promise.then 을 이용해서 처리하고 reject 됐을 때는 catch 로 잡아서 처리한다. 그리고 성공 실패 모두에서 실행되어야 할 코드가 있다면 finally 로 잡아서 처리하면 된다.

주의할게 then 으로 return 되는 객체 또한 promise 객체이기 때문에 then 을 몇 번씩 엮어서 사용할 수도 있다.

아래와 같은 콜백 지옥 함수가 있을 때 이를 프로미스로 해결해보자

```javascript
// 1. 콜백지옥
class UserStorage {
  loginUser(id, password, onSuccess, onError) {
    setTimeout(() => {
      if (
        (id === 'ellie' && password === 'dream') ||
        (id === 'coder' && password === 'academy')
      ) {
        console.log("successs");
        onSuccess(id);
      } else {
        console.log("fail");
        onError(new Error('not Found'));
      }
    }, 2000);
  }
  getRoles(user, onSuccess, onError) {
    setTimeout(() => {
      if (user === 'ellie') {
        onSuccess({ name: 'ellie', role: 'admin' });
      } else {
        onError(new Error('no Access'));
      }
    }, 1000);
  }
}

const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');
userStorage.loginUser(
  id,
  password,
  user => {
    userStorage.getRoles(
      user,
      userWithRole => {
        alert(`Hello ${userWithRole.name}, you have a ${userWithRole.role} role`)
      },
      erorr => { console.log("error"); }
    )
  },
  error => { console.log(error); }
)
```

```javascript
// 프로미스 사용
class UserStorage {
  loginUser(id, password) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (id === 'ellie' && password === 'dream') {
          resolve(id);
        } else {
          reject(new Error("No ID!!"));
        }
      }, 2000);
    })
  }

  getRoles(user) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (user === 'ellie') {
          resolve({ name: 'ellie', role: 'admin' });
        } else {
          reject(new Error('No Access'));
        }
      }, 1000);
    })
  }
}


const userStorage = new UserStorage();
const id = prompt('enter your id');
const password = prompt('enter your password');

userStorage
  .loginUser(id, password)
  .then(userStorage.getRoles) // resolve 인자 하나를 받아서 getRoles 에 그 인자를 바로 넣을 때는 생략가능
  .then((userWithRole => {
    alert(`Hello ${userWithRole.name}, you have a ${userWithRole.role} role`)
  }))
```

## async 와 await

> syntatic sugar. 새롭게 만들어진게 아니라 프로미스를 사용하기 쉽게 문법적으로 만든 APIs

### async

> 함수 앞에 async 를 사용하면 된다. 그러면 함수는 자연스럽게 Promise 를 리턴하게 된다.

```javascript
async function fetchUser() {
  // 만약 이게 실행시간이 10초라면...
  return 'ellie';
}
```

### await

> async 함수 내부에서만 사용된다. 이 키워드가 사용된 라인은 실제로 해당 라인이 리턴될 때 까지 기다린다.

```javascript
function delay(ms) {
  return new Promise(resolve => setTimeout( resolve, ms));
}

async function getApple() {
  await delay(3000);
  return 'apple';
}

async function getBanana() {
  await delay(3000);
  return 'banana';
}
```

이제 두개의 과일을 한번에 따는 경우를 생각해보자

```javascript
async function pickFruites() {
  // 프로미스는 생성하자 마자 바로 실행되는 원리를 이용!
  const applePromise = getApple();
  const bananaPromise = getBanana();
  const apple = await applePromise;
  const banana = await bananaPromise;
  return `${apple} + ${banana}`;
}
```

흠 이렇게 되면 뭔가 보기좋지 않아. 다른 방법을 찾자!

### Promise.all

```javascript
function pickFruites() {
  return Promise.all([getApple(), getBanana()])
    .then(fruits => fruits.join(' + '))
}
pickFruites().then(console.log);
```

Promise.all 로 인자로 들어온 모든 프로미스를처리할 수 있다.

만약 둘 중 먼저 리턴되는거 하나만 처리하고 싶다면?

### Promise.race

```javascript
function pickOnlyOne() {
  return Promise.race([getApple(), getBanana()]);
}

pickOnlyOne().then(console.log);
```

