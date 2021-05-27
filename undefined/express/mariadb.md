# mariadb 설치

## mariadb 설치

> 버전은 10.1 이상을 설치합니다. 5버전이 설치되는 환경이 있는데 그렇게 되면 시퀄라이즈 사용에 문제가 발생합니다.

### 로컬 및 EC2에 설치

> 이걸 보고 하자!!!!!!

[허광남 멘토님이 작성하신 글](https://okdevtv.com/mib/mariadb) 를 따라서 설치.

### 도커로 설치

> 컴포즈 파일을 작성 후에 `docker-compose up` 을 실행하면 빌드 후 자동으로 실행됩니다. 42서울 클러스터에서 마리아DB 설치가 어려워서 도커를 사용했습니다.

#### 컴포즈 파일

[링크를 참고해서 작성합니다.](https://int-i.github.io/sql/2020-12-31/mysql-docker-compose/)

이미지의 버전을 명시해주지 않으면 알아서 최신으로 다운받아집니다. 꼭 10.4 버전을 명시해줍시다.

### 주의할 점

* 한글이 저장이 안된다면 [블로그](https://kyome.tistory.com/134) 를 따라서 현재 `mariadb` 에서 사용하고 있는 `character-set` 이 무엇인지 확인합니다. `latin` 이 있다면 블로그를 따라 `utf-8`로 설정해줍시다.
* 로컬에 다른 DB가 켜져있으면 포트 문제로 실행되지 않습니다. 다른 DB를 사용중지 해주셔야합니다.
* `my.cnf` 파일을 수정한 경우에는 설정이 적용되지 않는 경우가 있습니다. 이 경우에는 사용하는 db 를 드랍하고 다시 생성해야합니다.
  * `drop database slap`
  * `create database slap`

## db 사용

### 1. 도커접속

1. `docker-compose up` 으로 DB 실행
2. `docker ps` 로 `db`  실행중인 컨테이너 찾기
3. `docker exec -it [container-id]  /bin/bash` 명령어로 컨테이너 접속

### 2. db 실행

1. `mysql -u[username] -p[password]` 로 db 접속
2. `show databases` 명령어로 데이터베이스 확인하기
   * 도커컴포즈로 실행하면 기본적으로 `.env` 설정파일의 `MYSQL_DATABASE` 의 값을 이름으로 데이터베이스가 생성됨
   * `create database [db-name]` 으로 데이터베이스 생성
   * `drop database [db-name]` 으로 데이터베이스 제거
3. `use [database name]` 으로 db 선택
4. `use tables` 으로 만들어진 모든 테이블 확인
5. `desc [table-name]` 으로 테이블의 구조 확인 가능
6. `select * from authors` 처럼 sql 명령어로 실행할 수 있음

