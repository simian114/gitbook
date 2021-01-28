# ESlint

[참고 사이트](https://travishorn.com/setting-up-eslint-on-vs-code-with-airbnb-javascript-style-guide-6eb78a535ba6)

## ESlint

* `linting` 이란 코드를 분석하는 프로그램으로 코드를 분석해 에러가 날만한 발견하고 에러를 발생시켜준다.
* 플러그인을 더해서 협업에도 잘 사용할 수 있게 만들수있다. 특히 `Airbnb` 의 코드 스타일이 많이 사용되고 우리 팀도 사용하기로 했으므로 `lint` 를 설치하고 `Airbnb` 플럭인을 더해주자

## ESlint 설치 및 적용

1. `vscode` 플러그인 상점에서 `ESlint` 를 설치한다.
2. **작업 프로젝트 루트 폴더** 로 이동후 아래 코드를 순서대로 입력해주자

   ```text
   npm init -y
   npm i -D eslint eslint-config-airbnb-base eslint-plugin-import
   touch .eslintrc.js
   ```

   ```javascript
   // .eslintrc.js
   module.exports = {
     "extends": "airbnb-base"
   };
   ```

3. `package.json` 을 확인해보면 디펜던시로 `eslint` 가 적용되어있는걸 확인할 수 있다.

   ```javascript
   ... 
   "devDependencies": {
       "eslint": "^7.18.0",
       "eslint-config-airbnb-base": "^14.2.1",
       "eslint-plugin-import": "^2.22.1"
     }
   }
   ```

4. 이제 `vscode` 를 재시작하자!

