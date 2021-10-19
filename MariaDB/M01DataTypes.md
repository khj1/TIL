# MariaDB의 자료형
MariaDB의 데이터 타입은 Oracle에 비해 매우 세분화 되어있다.
## 숫자형
- TINYINT
- SMALLINT
- MEDIUMINT
- INT
- BIGINT
- FLOAT
- DOUBLE
- DECIMAL(m,n)
  - 고정 소수
- BIT
### 숫자형 데이터와 관련된 주요 제약조건
- ```AUTO_INCREMENT```
  - 테이블 생성 시 자동 증가 컬럼으로 지정할 때 사용하는 제약 조건.
  - 오라클은 테이블과 별도로 시퀀스를 생성하지만 MySQL의 경우는 테이블의 특정 컬럼에 지정해줄 수 있다.
- ```UNSIGNED```
  - 숫자 컬럼인 경우 주로 -100 ~ 100 과 같은 형태로 범위가 지정된다.
  - ```UNSIGNED```는 음수는 사용하지 않고 양수만 사용하고 싶을 때 지정하는 제약 조건이다.
  - 단, 지정 시 제거된 음수의 범위만큼 양수의 범위가 2배로 늘어나게 된다.

## 문자형
- CHAR(n)
- VARCHAR(n)
  - 비교적 짧은 문자 데이터를 입력받을 때 자주 사용한다.
- BINARY(n)
- VARBINARY(n)
- TINYBLOB
- BLOB
  - BLOB이란 Binary Large Object를 의미한다.
  - BLOB의 경우 바이너리 데이터이므로 이미지 등의 파일들을 저정 할 수 있다.
  - 단 처리 성능이 좋지 않으므로 사용에 주의한다.
  - BLOB 필드는 정렬이나 INDEX 생성이 불가능하다.
- MEDIUMBLOB
- LONGBLOB
- TINYTEXT
- TEXT
  - 비교적 긴 글을 입력받을 때 사용된다.
- MEDIUMTEXT
- LONGTEXT
## 날짜형
- DATE
- DATETIME
- TIMESTAMP
- TIME
- YEAR
### 현재날짜
- 테이블 생성 시 혹은 데이터 삽입 시 오라클에서는 sysdate를 사용한다.
- MySQL
  - 데이터 삽입 시 ```NOW()``` 함수를 사용해서 현재 시간을 가져온다.
  - 테이블 생성 시 ```CURRENT_TIMESTAMP```를 default 값으로 사용한다.
### 날짜 및 시간 변환 함수
- 오라클에서는 ```TO_CHAR()```를 주로 사용했다.
- MySQL에서는 ```DATE_FORMAT(컬럼명,'서식')```을 사용한다.
    ```sql
    SELECT DATE_FORMAT(DATE1, '%Y-%m-%d') FROM tb_date;
    SELECT DATE_FORMAT(DATE2, '%H:%i:%s') FROM tb_date;
    ```
## 특수형
- enum
  - ```enum('A','B','C')```
  - 목록 중에서 하나를 선택할 수 있다.
  - ORACLE의 CHECK 속성 역할을 수행한다.
- set
  - ```set('A','B','C')```
  - 목록 중에서 여러개를 선택할 수 있다.
  - INSERT 시에는 ,(콤마)로 구분하여 여러개를 입력할 수 있다.

## 부록
- ```ON DELETE CASCADE```
  - 외래키 지정 시 부모 테이블의 레코드를 삭제하면 해당하는 자식 레코드도 모두 삭제되도록 해주는 속성이다.