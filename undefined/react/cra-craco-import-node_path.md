# CRA에서 CRACO 사용하지 않고 절대경로 import\(NODE\_PATH\)

1. `jsconfig.json`에 아래의 코드를 작성해준다. 해당 파일은 `.gitignore` 에 추가하면 안된다! 추후 배포과정에서 문제 생긴다!!

   ```javascript
   {
     "compilerOptions": {
       "baseUrl": "src"
     },
     "include": [
       "src"
     ]
   }
   ```

2. `package.json`을 수정해준다.

   ```javascript
   "scripts": {
     "start": "NODE_PATH=src react-scripts start",
     "build": "NODE_PATH=src react-scripts build",
   },
   ```

   `package.json`을 수정하는 것 말고도 `.env` 파일을 수정해서 하는 방법도 있다.

#### 

