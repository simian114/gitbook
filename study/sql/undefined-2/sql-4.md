---
description: 'ANY, ALL, SELECT INTO, INSERT INTO SELECT 를 학습합니다.'
---

# SQL 명령어 실습 - 4

### ANY and ALL Operator

* ANY와 ALL 는 WHERE와 HAVING 구에서 사용된다.
* ANY 는 서브쿼리 중에 조건에 맞는게 하나라도 있다면 true 를 리턴한다.
* ALL은 서브쿼리의 모든게 조건에 맞아야지 true를 리턴함

#### **SYNTAX**

```sql
SELECT column
FROM table
WHERE column operator(=, <>, > ..) [ANY, ALL]
  (SELECT column FROM table WHERE condition);
```

> Note: The operator must be a standard comparison operator \(=, &lt;&gt;, !=, &gt;, &gt;=, &lt;, or &lt;=\).

#### **EXAMPLE**

```sql
SELECT ProductName
FROM Products
WHERE ProductID = any (SELECT ProductID FROM OrderDetails WHERE Quantity = 10);
```

`ProductName`을 출력하는데 그 조건은 해당 판매량이 10인 상품이다. 판매량을 알기 위해서는 `OrderDetails`을 알아야 하기 때문에 서브쿼리를 이용했다. 만약 여기서 `any`가 아닌 `all`을 사용하게 되면 출력결과는 아무것도 존재하지 않는다. 그 이유는 `ProductID` 가 서브쿼리의 결과로 생기는 모든 값과 같아야 하기 때문.

### SELECT INTO

`SELECT INTO`는 테이블의 데이터를 복사해서 새로운 테이블에 넣는다. `IN`의 인자로는 데이터베이스의 이름이 온다. 즉, 현재 데이터베이스에 존재하지 않는 테이블에도 데이터를 복사할 수 있다는 것.

**SYNTAX**

> Copy all columns into a new table

```sql
SELECT *
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition;
```

> Copy only some columns into a new table

```sql
SELECT column1, column2, column3, ...
INTO newtable [IN externaldb]
FROM oldtable
WHERE condition;
```

**EXAMPLE**

```sql
SELECT CustomerName, ContactName INTO CustomerBackUp
FROM Customers;
```

### INSERT INTO SELECT

`INSERT INTO SELECT`는 어떤 테이블에 있는 데이터를 다른 테이블로 복사한다.

* `INSERT INTO SELECT`는 SOURCE 테이블과 TARGET 테이블의 `data type`이 같아야 한다.
* `TARGET` 테이블에 이미 존재하는 `column`들은 영향을 받지 않는다.

  **SYNTAX**

  > Copy all column from one table to another table

```sql
INSERT INTO table2
SELECT * FROM table1
WHERE condition;
```

> Copy ony some columns from one table into another table

```sql
INSERT INTO table2 (column1, column2, column3, ...)
SELECT column1, column2, column3, ...
FROM table1
WHERE condition;
```

**EXAMPLE**

```sql
INSERT INTO Customers (CustomerName, City, Country)
SELECT SupplierName, City, Country FROM Suppliers
WHERE Country='Germany';
```

