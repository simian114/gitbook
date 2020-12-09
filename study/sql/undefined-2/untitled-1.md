---
description: 'JOIN, UNION, GROUP BY, HAVING 에 대해 학습합니다.'
---

# SQL 명령어 실습 - 2

### JOIN

> JOIN을 사용하면 복수의 테이블을 하나로 결합할 수 있다.

JOIN은 세로로 테이블을 합치는 방식이 아닌 가로로 합치는 방식이다\(행이 아닌 열\).

JOIN으로의 결합은 총 네가지가 있다.

1. INNER JOIN

   INNER JOIN은 합치려고 하는 A, B 두 테이블에서 공통으로 존재하는 행에 합친다. 만약 둘 중 하나에만 존재하는 행 이라면 그 행은 제외된 상태로 결합이 된다.

2. LEFT JOIN

   JOIN은 FROM구\(A\)와 JOIN구\(B\)에 각각 테이블을 입력하는데, LEFT JOIN은 FROM구에 들어오는 테이블에 존재하는 모든 행을 포함하는 형태로 결합을 시킨다. 만약 A테이블에는 존재하지만 B테이블에는 없는 데이터라면 JOIN의 결과로 없는 내용에 대해서는 NULL 값이 들어오게 된다.

3. RIGHT JOIN

   LEFT JOIN과 정반대다. JOIN구에 들어오는 데이터를 중심으로 결합이 된다.

4. FULL OUTER JOIN

   이름 그대로 FULL JOIN이 된다.

기본적인 사용 방법은 아래와 같다.

```text
SELECT Orders.OrderID, Customers.CustomerName
FROM Orders    
    [OPTION]JOIN Customers ... OPTION - (INNER, LEFT, RIGHT, FULL)
        on Orders.CustomerID = Customers.CustomerID
```

### UNION

> UNION Operator는 두 개 이상의 SELECT구의 결과 셋을 합치는데 사용된다.

* UNION되는 SELECT의 결과는 반드시 같은 수의 열 개수를 가져야한다.
* 또한 `similar` 한 데이터 타입이어야 한다.
* `must same order`
* 만약 공통되는 값이 있다면 중복은 사라진다. - 합집합의 성질을 갖고있다.
  * 만약 중복되는 값도 모두가 출력되길 원한다면 ALL 옵션을 주면된다.

사용 방법은 아래와 같다. 주의 점은 `;` 세미콜론을 마지막에 한번만 입력해야 한다.

```text
SELECT column1 FROM table1
UNION (ALL)
SELECT column2 FROM table2;
```

### GROUP BY

GROUP BY 구는 `rows` 를 기준으로 group을 만든다. 이 구는 주로 `COUNT`, `MAX`, `MIN` 등과 같은 집계\(aggregate\) 함수들과 같이 쓰인다. \(집계함수와 사용하지 않으면 별로 의미가 없다.\)

```text
SELECT column
FROM table
WHERE contition
GROUP BY column;
```

만약 각 나라별로 고객이 얼마나 있는지를 알고 싶다면 _**나라**_ 를 기준으로 `GROUP` 으로 만들고 `COUNT` 함수를 사용하면 된다.

```text
SELECT Country, COUNT(CustomerID) as num
FROM Customers
    GROUP BY Country
    ORDER BY COUNT(CustomerID) DESC;
```

### HAVING

`HAVING` 구는 `WHERE` 구가 `aggregate` 함수들과 사용되지 못한다는 문제 때문에 생겼다.

```text
SELECT column
FROM table
WHERE condition
GROUP BY column
HAVING condition
ORDER BY column;
```

**WHERE과 ORDER BY 는 같이 사용하지 못한다?**

```text
SELECT name, COUNT(name)
FROM sample
WHERE COUNT(name) = 1 GROUP BY name;
```

위의 명령은 실행되지 않는다! 이유는 내부적으로 처리되는 순서에 이슈가 있기 때문. 아래는 내부 처리 순서다.

```text
WHERE 구 -> GROUP BY 구 -> SELECT 구 -> ORDER BY 구
```

따라서 우리가 원하는 대로 `name` 이 하나인 레코드를 출력하고 싶다면 `HAVING` 구를 사용하면 된다.

```text
SELECT name, COUNT(name)
FROM sample
GROUP BY name
HAVING COUNT(name) = 1;
```

