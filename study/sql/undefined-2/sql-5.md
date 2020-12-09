---
description: 'CASE, NULL에 대해 학습합니다.'
---

# SQL 명령어 실습 - 5

### CASE

CASE는 조건을 지나다가 첫 번째로 맞는 조건이 나오면 그 때 return 한다. 즉 해당 라인의 조건에 맞지 않는다면 return 되지 않고 다음 조건으로 넘어간다. 조건을 명시할 때는 `WHEN`을 그에 대한 return 값은 `THEN`으로 나타낸다. `WHEN` 절에는 참과 거짓을 반환하는 조건식을 기술한다. 마지막의 `ELSE`는 생략이 가능하다. 다만 생략하면 `ELSE NULL` 로 간주된다. `CASE`는 `NULL`을 처리하는 경우에 잘 사용될 수 있다.

#### SYNTAX

```sql
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    WHEN conditionN THEN resultN
    ELSE result
END;
```

#### EXAMPLE

```sql
SELECT OrderID, Quantity,
CASE
    WHEN Quantity > 30 THEN 'The quantity is greater than 30'
    WHEN Quantity = 30 THEN 'The quantity is 30'
    ELSE 'The quantity is under 30'
END AS QuantityText
FROM OrderDetails;
```

CASE 문으로 하나의 열을 만들었다. 주의할 것은 `CASE`의 마지막으로 `END` 키워드를 붙인다는 것과 `AS`를 이용해서 alise를 만들어 주는것. 만약 `AS`를 사용하지 않는다면 세 번째 열의 이름은 `case when Quantity > 30 then 'over 30' when Quantity = 30 then '30' else 'under 30' end` 이 된다.

```sql
SELECT a AS "코드",
CASE a
  WHEN 1 THEN 'MAN'
  WHEN 2 THEN 'WOMAN'
  ELSE '??'
END AS "SEX" FROM table
```

위와 같은 예시도 가능하다. 위의 경우는 2개의 열이 생긴다. 첫 번째 열은 `a`고 두 번째 열은 `a`열에 따른 성별이 오게 된다. 첫 번째 예시와 다른 건 `CASE` 바로 뒤에 `a` 가 온다는 것과 `WHEN` 조건문 뒤에 연산자가 없고 값만 있는 것. 해석하면 `a`가 1 이라면 남자, 2면 여자 그것도 아니면 `ELSE`로 가라는 의미다.

```sql
SELECT CustomerName, City, Country
FROM Customers
ORDER BY
(CASE
    WHEN City IS NULL THEN Country
    ELSE City
END);
```

`CASE`가 SELECT 구에서만 쓰이는게 아니라는 걸 보여주는 예시다. 이 경우는 쿼리의 결과를 정렬하는 것인데 `CITY` 의 값에 따라 정렬의 조건을 정한 것이다. 앞서 말했다시피 `NULL` 인 경우를 고려할 수 있는 방법이다.

### ISNULL

| id | ProductName | UnitPrice | UnitInStock | UnitsOnOrder |
| :--- | :--- | :--- | :--- | :--- |
| 1 | jang | 10.12 | 3 | 10 |
| 2 | san | 4 | 1 | NULL |
| 3 | nam | 5 | 23 | 23 |

위와 같은 테이블이 있을 때 쿼리를 날려보자.

```sql
SELECT ProductName, UnitPrice * (UnitInStock + UnitsOnOrder)
FROM Products
```

중간에 `NULL` 값이 있으므로 결과는 우리의 생각과는 조금 다르게 나온다. 따라서 `NULL` 인경우에 대해서 조금은 특별한 함수를 사용해줘야 한다. 바로 `ISNULL`

#### SYNTAX

```sql
ISNULL(Columns, value)
```

만약 해당하는 셀의 값이 `NULL`이라면 지정해준 `VALUE`가 된다. 이제 첫 번째 예시의 쿼리를 수정해주자.

#### EXAMPLE

```sql
SELECT ProductName, UnitPrice * (UnitInStock + ISNULL(UnitsOnOrder, 0))
FROM Products
```

