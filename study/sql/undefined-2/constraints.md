---
description: 제약들에 대해 학습합니다.
---

# Constraints

`Constraints` 는 테이블의 `data` 특정한 제약을 걸 때 사용한다.

### Create Constraints

테이블을 생성할 때, `ALTER` 를 이용해서 열을 수정할 때 제약을 생성할 수 있음.

#### SYNTAX

```sql
CREATE TABLE table_name (
    column1 datatype constraint,
    column2 datatype constraint,
    column3 datatype constraint,
    ....
);
```

#### Constraints의 종

1. `NOT NULL`: 해당 열에 `NULL` 값이 없다는 걸 보장하게 해준다.
2. `UNIQUE`: 해당 열에 중복이 없게 만든다.
3. `PRIMARY KEY`: `기본키`라고도 한다. `NOT NULL`과 `UNIQUE`의 조합이 된다. 보통 이 기본키를 이용해서 특정 행을 지정한다.
4. `FOREIGN KEY`: 다른 테이블의 `기본키`
5. `CHECK`: 해당 값에 조건을 건다.
6. `DEFAULT`: 값이 정해지지 않았을 때 세팅해 줄 기본 값.
7. `INDEX`: 데이터 베이스에 행을 빠르게 생성하거나 삭제할 때 사용함. 이진탐색트리

