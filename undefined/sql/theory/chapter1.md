# 1강 데이터베이스

## 데이터베이스

> 데이터란 컴퓨터 안에 기록되어 있는 수자를 의미하며, 이러한 데이터의 집합을 **데이터베이스**라고 한다. 넓은 의미에서는 컴퓨터 안에 기록된 모든 것을 데이터베이스라고도 학 수 있다. 일반적으로 통용되는 데이터베이스라는 개념은 특정 데이터를 확인하고 싶을 떄 간단하게 찾아낼 수 있도록 정리된 형태를 가리킨다.

### 1. 시스템 내의 데이터베이스

얼마전까지만 해도 데이터베이스는 데이터센터의 서버에서 운용되었다. 하지만 최근에는 개인용컴퓨터, 휴대용 기기에도 내장되어 있다.

### DB와 DBMS

데이터베이스는 일반적으로 _**DB**_라는 약자로 통용된다. 데이터베이스는 저장장치 내에 정리되어 저장된 데이터의 집합이고, 이를 효율적으로 관리하는 소프트웨어를 **데이터베이스 관리 시스템** 줄여서 _**DBMS**_ 라고 한다.  
그렇다면 왜 DBMS 와 같은 전용 소프트웨어가 필요할까&gt;

1. 생산성
2. 기능성
3. 신뢰성
   * 대규모 데이터베이스는 많은 요청에 대응할 수 있도록 만들어져 있다. 이를 위해 하드웨어를 여러 대로 구성하여 신뢰성을 높이는 동시에 성능 향상을 꾀하기도 한다.

### 데이터베이스를 조작하는 언어

DBMS는 데이터베이스를 관리하는 소프트웨어다. DBMS를 이용하면 간접적으로 데이터베이스를 참조할 수 있고, 혹은 데이터를 추가하거나 삭제, 갱신할 수도 있다. 이런 DBMS와의 대화에 필요한 것이 _**SQL**_ 이다. SQL는 ISO 등에 의해 표준화가 진행되어 C나 자바와 마찬가지로 표준 언어이다.

#### SQL 명령의 종류

1. DML

   Data Manipulation Language의 약자. DB에 새롭게 데이터를 추가하거나 삭제하거나 내용을 갱신하는 등, 데이터를 조작할 때 사용한다. SQL의 가장 기본이 되는 명령set 이다.

2. DDL

   Data Definition Language 의 약자. DB는 데이터 베이스 객체 라는 데이터 그릇을 이용하여 데이터를 관리하는데, 이런 객체를 만들거나 삭제하는 명령어다.

3. DCL

   Data Control Language 의 약자. 데이터를 제어하는 명령어. DCL에는 트랜잭션을 제어하는 명령과 데이터 접근 권한을 제어하는 명령이 포함되어 있다.
