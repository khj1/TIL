# MariaDB
## DB 생성 및 사용자 계정 생성
### 데이터 베이스 확인
- ```SHOW DATABASES;```
### 데이터 베이스 생성
- ```CREATE DATABASE 데이터베이스 이름;```
- 오라클은 계정만 생성하여 접속하는 형태지만, MariaDB는 DB 계정을 생성하여 접속해야한다.
### 데이터 베이스 사용
- ```USE 데이터베이스 이름;```
- DB 계정이나 사용자 계정은 모두 mysql DB에서 관리된다. ```USE mysql;```
### 사용자 확인
- ```SELECT host, user, password FROM user;```
- 사용자 계정은 user 테이블에서 관리된다.
### 사용자 계정 생성
- ```CREATE USER '사용자계정명'@'localhost'[또는 %] IDENTIFIED BY '비밀번호'```
- 특정 DB에 접근할 수 있는 사용자 계정을 만든다.
- 로컬에서만 접속할 때는 ```'사용자계정명'@'localhost'``` 형태로 생성한다.
- 만약 외부에서도 접근해야 한다면 '%'를 사용한다.
- root 계정은 모든 DB에 접근할 수 있는 권한을 가진다.
### 사용자 권한 주기
- ```GRANT ALL PRIVILEGES ON 데이터베이스명.* TO '사용자계정명'@'localhost';```
- 데이터베이스명.* : 해당 DB의 모든 테이블을 의미한다.
### 새로고침, 변경 내용 적용
```FLUSH PRIVILEGES;```
### 사용자 계정 삭제
```DROP USER '사용자계정명'@'localhost'```
### user 계정으로 접속하기
- ```mysql -u 사용자계정 -p 접속할DB명```