# PostgreSQL 시작하기 [![author](https://img.shields.io/badge/author-devuri-4c80f1.svg?style=flat-square)](https://github.com/zoripong)
## PostgreSQL?
- 객체지향 데이터베이스 [(ORDBMS)](https://ko.wikipedia.org/wiki/%EA%B0%9D%EC%B2%B4_%EA%B4%80%EA%B3%84_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)
- [MySQL vs PostGIS (Postgresql) 범위 질의 성능 비교](https://steemit.com/kr-dev/@tmkor/db-2-mysql-vs-postgis-postgresql)
- [PostgreSQL과 MariaDB(MySQL) 성능 차이](https://blog.naver.com/PostView.nhn?blogId=malloc813&logNo=220465183659&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)

## Envirionment
- psql (PostgreSQL) 9.5.14
- Linux Ubuntu 16.04
- Python 3.5.2

## Installation
### PostgreSQL Server
[Zeta-Wiki](https://zetawiki.com/wiki/%EC%9A%B0%EB%B6%84%ED%88%AC_PostgreSQL_%EC%84%A4%EC%B9%98)를 참고하였습니다.
> 기존에 postgresql이 설치되어있는지 확인합니다.
```
$ aptitude show postgresql | grep State
```

> postgresql을 설치합니다.
```
$ sudo apt-get install postgresql
```

> 정상적으로 설치가 완료되었다면 관리자 계정 postgres가 생성되어있습니다.
```
$ cat /etc/passwd | grep postgres
```

> 현재 postgres의 네트워크 상태를 확인할 수 있습니다. 
 설치 직후, 로컬에서만 접속할 수 있도록 설정 되어 있습니다.
```
$ sudo netstat -tnlp | grep postgres
```
> 패스워드 및 접속 IP 대역 설정
```
$ sudo -u postgres psql template1
template1=# ALTER USER postgres with encrypted password 'P@ssw0rd';
template1=# \q
```

> 설정파일 위치 확인
```
$ ps -ef | grep postgresql.conf | grep -v grep
 ```
 
> 설정 파일 변경 
- postgresql.conf 변경
```
$ sudo vi /etc/postgresql/9.5/main/postgresql.conf
```
[추가]
```
listen_addresses = '*'
```
- pg_hba.conf 변경
```
$ sudo vi /etc/postgresql/9.5/main/pg_hba.conf
```
[추가]
```
host    all             all             0.0.0.0/0               md5
```
[변경]
```
local   all             postgres                                peer
=========>
local   all             postgres                                md5
```
> DB 서버 재시작
```
$ sudo /etc/init.d/postgresql restart
```
> 제대로 적용 되었나 다시 한 번 확인
```
$ sudo netstat -tnlp | grep postgres
```
### PostgreSQL Client
> 설치 확인
```
$ psql -V
```
> 미설치시 
```
$ sudo apt-get install postgresql-client
```

> 접속
``` 
$ psql postgres -U postgres
```
> 종료
```
postgres=# \q
```
## Before Starting
1. [데이터타입](https://www.tutorialspoint.com/postgresql/postgresql_data_types.htm)
    - 숫자
    - 화폐 (?)
    - 문자열
    - 2진 데이터
    - 날짜/시간
    - Boolean
    
   외 매우 많은 타입을 포함합니다. (JSON, Array, Network Address, Geometric, Enumerated)
1. [제약조건](https://www.tutorialspoint.com/postgresql/postgresql_constraints.htm)
1. [연산자](https://www.tutorialspoint.com/postgresql/postgresql_operators.htm)

## command
> 연결된 데이터베이스의 모든 테이블을 나열하는 데 사용됩니다.
```
testdb-# \d
```
> 테이블 구조 확인
```
testdb-# \d [table name]
```
> 

## Syntax
### 1. Create Table
```
CREATE TABLE table_name(
   column1 datatype,
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
   PRIMARY KEY( one or more columns )
);
```
### 2. Drop Table
```
DROP TABLE table_name;
```
### 3. Create Schema
#### Schema?
- 이름이 지정된 테이블의 집합
- 보기, 인덱스, 시퀀스, 데이터유형, 연산자 기능 포함 가능
- 운영체제 수준의 디렉토리와 유사
- 스키마 중복 불가
```
CREATE SCHEMA name;
```
### 4. Insert
```
INSERT INTO TABLE_NAME (column1, column2, column3,...columnN)
VALUES (value1, value2, value3,...valueN);
```
```
INSERT INTO TABLE_NAME VALUES (value1,value2,value3,...valueN);
```

### 5. Select 
```
SELECT column1, column2, columnN FROM table_name;
```
```
SELECT * FROM table_name;
```


### 6. Where
> Basic Syntax
```
SELECT column1, column2, columnN
FROM table_name
WHERE [search_condition]
```
- [AND 연산자](https://www.tutorialspoint.com/postgresql/postgresql_and_or_clauses.htm)
- [OR 연산자](https://www.tutorialspoint.com/postgresql/postgresql_and_or_clauses.htm)
- IS NULL / IS NOT NULL 연산자
- [LIKE 연산자](https://www.tutorialspoint.com/postgresql/postgresql_like_clause.htm)
- IN / NOT IN 연산자
- BETWEEN 연산자
- EXIST 연산자
- SUBQUERY

**Exapmle>**

```
testdb=# SELECT * FROM COMPANY WHERE AGE >= 25 AND SALARY >= 65000;
testdb=# SELECT * FROM COMPANY WHERE AGE >= 25 OR SALARY >= 65000;
testdb=# SELECT * FROM COMPANY WHERE AGE IS NOT NULL;
testdb=# SELECT * FROM COMPANY WHERE NAME LIKE 'Pa%';
testdb=# SELECT * FROM COMPANY WHERE AGE IN ( 25, 27 );
testdb=# SELECT * FROM COMPANY WHERE AGE BETWEEN 25 AND 27;
testdb=# SELECT AGE FROM COMPANY WHERE EXISTS (SELECT AGE FROM COMPANY WHERE SALARY > 65000);
testdb=# SELECT * FROM COMPANY WHERE AGE > (SELECT AGE FROM COMPANY WHERE SALARY > 65000);
```

### 7. Update
```
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
```

### 8. DELETE
```
DELETE FROM table_name
WHERE [condition];
```

### 9. Group by
```
SELECT column-list
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2....columnN
ORDER BY column1, column2....columnN
```

### 10. Having
```
SELECT column1, column2
FROM table1, table2
WHERE [ conditions ]
GROUP BY column1, column2
HAVING [ conditions ]
ORDER BY column1, column2
```

### 11. Join
#### Cross Join (교차조인)
```
SELECT ... FROM table1 CROSS JOIN table2 ...
```
두 테이블의 카디션 프로덕트를 구하는 연산
#### Inner Join (내부조인)
```
SELECT table1.column1, table2.column2...
FROM table1
INNER JOIN table2
ON table1.common_filed = table2.common_field;
```
- 두 테이블을 결합하여 새로운 결과 테이블을 만듦.
- 각 행과 비교하여 조인 한정자를 충족하는 모든 행 쌍을 찾음.
- 가장 일반적인 유형의 조인으로 키워드 생략 가능

#### Left Outer Join (왼쪽 외부 조인)
```
SELECT ... FROM table1 LEFT OUTER JOIN table2 ON conditional_expression ...
```
- 좌측 테이블의 모든 데이터를 포함하는 결과 집합 생성
- table1 손실 X 
#### Right Outer Join (오른쪽 외부 조인)
```
SELECT ... FROM table1 RIGHT OUTER JOIN table2 ON conditional_expression ...
```
- 우측 테이블의 모든 데이터를 포함하는 결과 집합 생성
- table2 손실 X 

#### Full Outer Join (완전 조인)
```
SELECT ... FROM table1 FULL OUTER JOIN table2 ON conditional_expression ...
```
- Left + Outer Join


### etc
- [Limit](https://www.tutorialspoint.com/postgresql/postgresql_limit_clause.htm)
- [Order by](https://www.tutorialspoint.com/postgresql/postgresql_order_by.htm)
- [With](https://www.tutorialspoint.com/postgresql/postgresql_with_clause.htm)
     - [With..As](http://brownbears.tistory.com/212)
- [Distinct](https://www.tutorialspoint.com/postgresql/postgresql_distinct_keyword.htm)
- [CrossJoin vs Full Outer Join](https://code.i-harness.com/ko-kr/q/3144c7)
- [Auto Increment(Serial)](https://www.tutorialspoint.com/postgresql/postgresql_using_autoincrement.htm)
- [Flask-SQLAlchemy PostgreSQL](https://realpython.com/flask-by-example-part-2-postgres-sqlalchemy-and-alembic/)
## Reference
* Install on Linux Ubuntu
  - [ZetaWiki](https://zetawiki.com/wiki/%EC%9A%B0%EB%B6%84%ED%88%AC_PostgreSQL_%EC%84%A4%EC%B9%98)


* Basic Grammar
  - [Tutorial Point](https://www.tutorialspoint.com/postgresql/index.htm)
