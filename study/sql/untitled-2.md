# SQL 명령어 실습 - 1

[W3의 듀토리얼을 참고했습니다.](https://www.w3schools.com/sql/default.asp)

### **update**

```text
UPDATE Customers
SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
WHERE CustomerID = 1;
```

* 주의 할 점은 `where` 조건을 잘 사용해야 한다든 것.
* 만약 `where` 조건을 사용하지 않으면 `Customers` 테이블의 모든 `Record` 의 `ContactName`, `City` 값이 변경되어 버린다.
* 따라서 하나의 레코드만을 변경시킬 경우에는 primary 값을 이용하도록하자.

### **delete**

> **주의** delete를 사용할 때는 주의하자. 만약 where 명령어를 사용하지 않는다면 해당 테이블의 모든 레코드가 삭제되어 버린다.

```text
DELETE FROM table_name WHERE condition;
```

### **top**

* `select top` 은 return 될 레코드의 수를 지정한다.
* 이 명령은 레코드가 매우 많을 때 유용하게 사용될 수 있다.

```text
SELECT TOP 3 * FROM Customers;
```

* 또한 퍼센트의 개념으로도 사용될 수 있다.

```text
SELECT TOP 50 PERCENT * FROM Customers;
```

* 조건문과 사용하려면 아래처럼 하자

```text
SELECT TOP 3 * FROM Customers
WHERE Country='Germany';
```

### **min, max**

```text
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

* `AS` 를 사용하면 더 보기 좋은 결과를 얻을 수 있다.

```text
SELECT MIN(Prices) FROM Products;
```

```text
SELECT MIN(Pirces) AS SmallestPrice
FROM Products;
```

* `AS`가 없는 경우에는 쿼리 실행결과로 나오는 테이블의 열의 이름이 `MIN(Prices)`다. 보기 안좋음. 따라서 `AS` 로 바꿔주자.

### **like operator**

* Like operator 는 `Where clause` 특정한 패턴을 갖는 열을 찾을 때 사용된다.
  * `%` 는 0개 이상의 `char` 를 찾을 때 사용
  * '\_' 는 한개의 `char` 을 찾을 때 사용

```text
SELECT column1, column2, ...
FROM table_name
WHERE columnN LIKE pattern;
```

### **Wildcard Characters**

* `Wildcard character` 는 문자열에서 하나 이상의 `char` 를 대체할 때 사용된다.
* `like operator` 와 같이 사용된다.
  * 그리고 `like` 는 `where clause` 와 같이 사용됨

| Symbol | Description | Example |
| :--- | :--- | :--- |
| % | Represents zero or more characters | bl% finds bl, black, blue, and blob |
| \_ | Represents a single character | h\_t finds hot, hat, and hit |
| \[\] | Represents any single character within the brackets | h\[oa\]t finds hot and hat, but not hit |
| ^ | Represents any character not in the brackets | h\[^oa\]t finds hit, but not hot and hat |
| - | Represents a range of characters | c\[a-b\]t finds cat and dot |

* 모든 와일드카드들은 조합되서 사용될 수 있다.

### **In operator**

* In operator 은 `where clause` 를 사용할 때 다수의 values를 지정할 수 있게 만들어 준다.
* In operator 은 다수의 OR 조건의 짧은 버전이다.

```text
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

```text
SELECT column_name(s)
FROM table_name
WHERE column_name IN (SELECT STATEMENT);
```

### **Between operator**

* between operator 는 주어진 범위 안에 있는 값을 선택한다. 값들은 `numbers`, `text`, `dates` 등이 될 수 있다.
* 시작점은 포함하지만 끝은 포함하지 않는다. `[, )`

```text
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

### **Aliases**

* aliases 는 테이블, 또는 열에게 임시 이름을 준다.
* 일반적으로 열의 이름을 가독성있게 만들어준다.
* 지속적으로 남아있는게 아닌 하나의 쿼리에서 효력이 끝난다.

#### **Alias Column Syntax**

```text
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

#### **Alias Table Sybtax**

```text
SELECT column_name(s)
FROM table_name AS alias_name;
```

