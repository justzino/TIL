# SQL 한방에 끝내기


# 1. SQL의 분류

## DML

Data Manipulation Language

- 데이터 조작 언어
- `SELECT`, `INSERT`, `UPDATE`, `DELETE` (CRUD)

## DDL

Data Definition Language

- 데이터 정의 언어
- 테이블, 뷰, 인덱스 등의 database를 생성, 삭제, 변경
- `CREATE`, `DROP`, `ALTER`

## DCL

Data Control Language

- 데이터 제어 언어
- 권한 부여, 권한 제거
- `GRANT`, `REVOKE`

# SQL 명령어 연습


### 기본 명령어

```sql
SHOW DATABASES;
USE world;
SHOW TABLES;          # world 테이블 이름 보기
SHOW TABLE STATUS;    # 데이터베이스 world의 테이블 정보 조회
DESCRIBE city;        # city 테이블에 무슨 열이 있는지 확인
DESC city;
DESC country;
DESC countrylanguage;
```

### SELECT, FROM, WHERE, AND, BETWEEN, >, ≤, ==, ≠

```sql
SELECT Name, Population 
From city;

SELECT Name, Population
FROM city
WHERE Population > 8000000;

SELECT *
FROM city
WHERE Population < 80000000
AND Population > 7000000;

SELECT *
FROM city
WHERE Population
BETWEEN 7000000 AND 8000000;

# 한국에 있는 도시들 보기
SELECT *
FROM city
WHERE CountryCode = 'KOR';

# 미국에 있는 도시들 보기
SELECT *
FROM city
WHERE CountryCode = 'USA';

# 한국에 있는 도시들 중에 인구 수가 1,000,000 이상인 도시 보기
SELECT *
FROM city
WHERE CountryCode = 'KOR'
AND Propulation >= 1000000;
```

### IN

- 이산적인 값의 조건에서 사용가능

```sql
SELECT *
FROM city
WHERE Name IN('Seoul', 'New York', 'Tokyo');

# 한국, 미국, 일본의 도시들만 보기
SELECT *
FROM city
WHERE CountryCode IN('KOR', 'USA', 'JPN');
```

### LIKE

- 문자열의 내용을 검색하기 위해 사용

```sql
# abcd_ :한 글자만 매치
SELECT *
FROM city
WHERE CountryCode LIKE 'KO_';  # KOR

# abcd% : 문자 뒤에 무엇이든 허용
SELECT *
FROM city
WHERE Name LIKE 'Tel %';   # Tel Aviv-Jaffa
```

### Sub Query

- 쿼리문 안에 또 쿼리문이 들어 있는 것
- 서브 쿼리의 결과가 둘 이상이 되면 에러 발생
- 여러개의 결과가 나올 때는 **ANY**, **SOME**, **ALL** 등 사용

```sql
SELECT *
FROM city
WHERE CountryCode = ( SELECT CountryCode
                      FROM city
                      WHERE Name = 'Seoul' );

# ANY : 서브쿼리의 여러 개의 결과중 하나만 만족해도 가능 (=ANY 와 IN 은 동일)
SELECT *
FROM city
WHERE Population > ANY ( SELECT Population
                         FROM city
                         WHERE District = 'New York' );

# ALL : 서브쿼리의 여러 개의 결과를 모두 만족해야함
SELECT *
FROM city
WHERE Population > ALL ( SELECT Population
                         FROM city
                         WHERE District = 'New York' );
```

### ORDER BY

```sql
# ASC : 오름차순 (default 임)
SELECT *
FROM city
ORDER BY Population ASC;

SELECT *
FROM city
ORDER BY Population;

# DESC : 내림차순
SELECT *
FROM city
ORDER BY CountryCode ASC, Population DESC;

# 인구수로 내림차순하여 한국에 있는 도시 보기
SELECT *
FROM city
WHERE CountryCode = 'KOR'
ORDER BY Population DESC;

# 국가 면적 크기로 내림차순하여 보기
SELECT *
FROM country
ORDER BY SurfaceArea DESC;
```

### DISTINCT

- 중복된 것은 1개씩만 출력

```sql
SELECT DISTINCT CountryCode
From city;
```

### LIMIT

- 출력 개수를 제한
- 상위의 N개만 출력하는 '**LIMIT N**' 구문

```sql
SELECT *
FROM city
ORDER BY Population DESC
LIMIT 10;
```

### **Aggregate operator**

- **Aggregate operator**는 중첩(nested)될 수 없다!!
- 종류

```sql
# 도시는 총 몇개인가?
SELECT COUNT(*)
FROM city;

# 도시들의 평균 인구수
SELECT AVG(Population)
FROM city;
```

### GROUP BY, AS(Alias)

- 그룹으로 묶어주는 역할
- **Aggregate Function** (집계 함수)을 함께 사용

```sql
# 같은 CountryCode 를 갖는 city 중 Population 이 가장 큰 놈만 출력
SELECT CountryCode, Max(Population)
FROM city
GROUP BY CountryCode;

SELECT CountryCode, AVG(Population) AS 'Average'
FROM city
GROUP BY CountryCode;

```

### HAVING

- **WHERE** 과 비슷한 개념으로 조건 제한
- **Aggregate Function** (집계 함수)에 대해 조건을 제한할 때 사용
- 반드시 **GROUP BY** 절 이후에 나와야함

```sql
SELECT CountryCode, MAX(Population)
FROM city
GROUP BY CountryCode
HAVING MAX(Population) > 8000000;
```

### ROLLUP

- 총합 or 중간합계가 필요한 경우
- **GROUP BY** 절과 함께 **WITH ROLLUP** 문 사용

```sql
SELECT CountryCode, Name, SUM(Population)
From city
GROUP BY CountryCode, Name WITH ROLLUP;
```

- 결과 모습

    ![](./Images/db1.png)

    **CountryCode** 끝날때 마다 `SUM(Population)` 의 값이 있음

### JOIN

- 여러 테이블에서 가져온 record 들을 조합 → 하나의 테이블

```sql
SELECT *
FROM city
JOIN country ON city
```

### reference

[이수안컴퓨터연구소 유튜브]([https://www.youtube.com/watch?v=vgIc4ctNFbc](https://www.youtube.com/watch?v=vgIc4ctNFbc))

데이터베이스 시스템 3판_Raghu Ramakrishnan