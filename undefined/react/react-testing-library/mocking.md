# Mocking 하기

API 를 그냥 사용하면서 테스트 하면 3가지 문제점이있다.

1. Request 비용이 크다
2. 느리다
3. 테스트가 외부요인에 의존하게 된다.

따라서 테스트에서 사용하는 API 는 isolation 하게 만들어야한다. 바로 Mocking 하는것. 간단히 더미데이터를 보내준다고 생각하면된다.

모킹하는 순서는 아래와 같다.

1. 우선 `src` 폴더에 `__mocks__` 이름을 갖는 폴더를 만들어주자.
   *  폴더의 이름 규칙은 매우 중요하다!!
2. 비동기 처리 코드를 파일 이름으로 그대로 사용하자.
   * 예를 들면 `axios` 로 비동기 처리를 하면 `axios.js` 파일을 만들어준다.
3. export default 할 객체를 만든다.
4. 아래처럼 코딩해주자

```javascript
const mockResponse = {
  data: {
    results: [
      {
        name: {
          first: 'Laith',
          last: 'Harb',
        },
        picture: {
          large: 'https://randomuser.me/api/portraits/men/1.jpg',
          medium: 'https://randomuser.me/api/portraits/med/men/1.jpg',
          thumbnail: '"https://randomuser.me/api/portraits/thumb/men/1.jpg"',
        },
        login: {
          username: 'ThePhonyGOAT',
        },
      },
    ],
  },
};

const axios = {
  get: jest.fn().mockResolvedValue(mockResponse),
};

export default axios;
```

`jest.fn().mockResolvedValue` 안에 원하는 데이터를 넣으면 된다. 참고로 파일 구조는 아래와같다.

![\_\_mock\_\_ &#xC774;&#xB984; &#xADDC;&#xCE59;!](../../../.gitbook/assets/2021-07-20-9.25.42%20%281%29.png)

그리고 하나 더 해줘야할 것이, `mock` 리셋을 매번해줘야한다. 해주지 않으면 에러가 발생. 아직 원인은 잘 모르겠다.

```javascript
...
  "jest": {
    "collectCoverageFrom": [
      "src/**/*.{js,jsx,ts,tsx}"
    ],
    "resetMocks": false
  }
```

