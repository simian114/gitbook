---
description: DB를 생성하고 삭제하는 명령에 대해 학습합니다.
---

# DB 관리

### CREATE DATABASE

> 가장 기본적이면서 중요한 명렁

데이터베이스를 생성한다.

#### SYNTAX

```sql
CREATE DATABASE databasename;
```

### DROP DATABASE

데이터베이스를 삭제한다.

#### SYNTAX

```sql
DRIP DATABASE databasename;
```

### BACKUP DATABASE

데이터베이스를 통으로 백업한다. `TO DISK` 구에는 원하는 파일위치를 지정한다. `WITH` 옵션을 사용하면 백업되어있는 데이터베이스와의 차이점만을 백업한다. \(시간 절약에 좋음\)

#### SYNTAX

```text
BACKUP DATABASE databasename
TO DISK = 'filepath'
[WITH DIFFERENTIAL]
```

