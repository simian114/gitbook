---
description: '테이블의 생성과 삭제, 열을 관리하는 명령에 대해 학습합니다.'
---

# TABLE 관리

### CREATE TABLE

데이터베이스 안에 새로운 테이블을 생성한다.

#### SYNTAX

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    column3 datatype,
   ....
);
```

* `column` 은 테이블의 열 이름을 지정해준다.
* `datatype` 은 해당 열의 데이터 타입을 지정해 줌
  * `varchar`, `integer`, `date`, `text`, `etc..`

### CREATE TABLE USING ANOTHER TABLE

> 이미 존재하는 다른 테이블을 이용해서도 새로운 테이블을 만들 수 있다.

#### SYNTAX

```sql
CREATE TABLE new_table_name AS
    SELECT column1, column2,...
    FROM existing_table_name
    WHERE ....;
```

### DROP TABLE

데이터베이스에 존재하는 테이블을 삭제하는데 사용된다.

#### SYNTAX

```sql
DROP TABLE table_name;
```

> > 주의! 삭제관련 명령은 경고문이 나오지 않는다. 따라서 중요 데이터베이스도 삭제할 수 있는 위험이 있음.

### TRUNCATE TABLE

이미 존재하는 테이블을 비우는데 사용된다.

#### SYNTAX

```sql
TRUNCATE TABLE table_name;
```

### ALTER TABLE

`ALTER TABLE` 는 존재하는 테이블의 `열`을 `추가, 삭제 수정` 한다. 또한 `열` 에 걸려있는 제한\(`constraints`\)을 추가하거나 삭제하기도 한다.

### ADD Columns

#### SYNTAX

```sql
ALTER TABLE table_name
ADD column_name datatype;
```

### DROP Columns

#### SYNTAX

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

### MODIFY Columns

#### SYNTAX

```sql
ALTER TABLE table_name
ALTER COLUMN column_name datatype;
```

