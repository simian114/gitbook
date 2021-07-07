# CRA로 만든 앱에서 절대경로로 import 해오기\(alias하기\)

{% embed url="https://www.npmjs.com/package/@craco/craco" %}

[https://stackoverflow.com/a/65746792](https://stackoverflow.com/a/65746792)

## 문제

다른 경로의 모듈을 `import` 로 가져올 때 `../../.....` 처럼 상대경로로 하게 되면 뎁스가 너무 길어진다. 절대경로로 바꿔서 `alias` 해서 가져오는 방법은 없을까

## 해결

> CRA 로 만든 앱은 web pack 설정이 숨겨져있다. Eject 를 사용해서 따로 설정할 수 있지만, 굳이 그러지말자

**craco** 를 사용하자. `craco` 는 `Create React App Configuration Override` 의 줄임말이다. 이름 그대로 CRA의 설정을 덮어써준다.

### 사용

1. `npm install @craco/craco --save`
2. 루트위치에 `croco.config.js` 를 만들고 아래처럼 경로를 alias 하자.

   ```javascript
   const path = require('path');

   module.exports = {
     webpack: {
       alias: {
         '@': path.resolve(__dirname, 'src/'),
         '@components': path.resolve(__dirname, 'src/components'),
         '@shared': path.resolve(__dirname, 'src/components/shared'),
         '@constants': path.resolve(__dirname, 'src/constants'),
         '@views': path.resolve(__dirname, 'src/views'),
       },
     },
   };
   ```

3. `package.json` 파일을 수정해주자

   ```javascript
   /* package.json */

   "scripts": {
   -   "start": "react-scripts start",
   +   "start": "craco start",
   -   "build": "react-scripts build",
   +   "build": "craco build"
   -   "test": "react-scripts test",
   +   "test": "craco test"
   }
   ```

4. `alias`를 사용하자!

   ```javascript
   ...
   import Template from '@shared/Template/Template';
   ...
   ```

