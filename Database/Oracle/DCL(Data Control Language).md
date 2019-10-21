# DCL(Data Control Language)

USER에게 데이터베이스에 접근하고 객체들을 사용할 수 있는 권한을 주고 회수하는 명령어들을 말한다. GRANT, REVOKE 등이 있다.

## 권한(Privilege)

권한이란 테이블 스페이스를 만들고 그 곳에 접근할 수 있는 사용자를 만들었을 때 그 사용자가 할 수 있는 일들을 말한다. 시스템 권한과 객체 권한이 있다.

<br />

### 시스템 권한의 종류

```mariadb
create session
create table
create view ...
```

<br />

### 권한 생성

```mariadb
GRANT system_privilege TO [user_name | PUBLIC] [WITH ADMIN OPTION];
```

TO PUBLIC으로 지정하면 모든 USER가 해당 권한을 가진다.

`WITH ADMIN OPTION` 옵션을 사용하면 권한을 받은 USER가 다른 USER에게 해당 권한을 줄 수 있다.

```mariadb
GRANT create session TO test1 WITH ADMIN OPTION;
GRANT create table TO test1;

//test1 계정으로 접속
GRANT create session TO test2;
GRANT create table TO test2;	//에러 - test1은 다른 USER에게 'create table' 권한을 줄 수 있는 권한이 없음
```

<br />

### 권한 취소

```mariadb
REVOKE system_privilege [, sytem_privilege2...] FROM [user_name | PUBLIC];
```

해당 권한을 FROM으로 지정된 사용자로부터 삭제한다.

```mariadb
REVOKE create table, create session FROM test1;
```

<br />

### 객체 권한 지정

```mariadb
GRANT object_privilege (column1, column2 ...)
	ON [object_name | ALL]
	TO [user_name | PUBLIC]
	WITH GRANT OPTION;
```

객체 권한은 테이블이나 뷰, 시퀀스, 프로시저, 함수 또는 패키지 중 지정된 한 객체에 특별한 작업을 수행할 수 있는 권한을 말한다. `WITH GRANT OPTION` 옵션을 사용하면 해당 USER가 다른 USER에게 권한을 부여할 수 있다.

*PUBLIC으로 권한을 부여하면 PUBLIC으로 회수해야 한다.*

```mariadb
GRANT SELECT ON emp TO test1 WITH GRANT OPTION;
GRANT INSERT(empno, ename), UPDATE(ename) ON emp TO test1 WITH GRANT OPTION;

//test1 계정으로 접속
GRANT SELECT ON emp TO test2;
```

test1에게 emp 테이블에 SELECT, INSERT, UPDATE 할 수 있는 권한을 부여했다. 이때 INSERT와 UPDATE 권한은 지정된 컬럼에 한정된다. test1도 다른 USER에게 그 권한을 부여할 수 있다.

<br />

### 객체 권한 회수

```mariadb
REVOKE object_privilege
	ON [object_name]
	FROM [user_name | PUBLIC]
	CASCADE CONSTRAINTS;	//참조 객체 권한에서 사용된 참조 무결성 제한을 함께 삭제
```

`WITH GRANT OPTION` 으로 객체 권한을 부여한 사용자의 객체 권한을 철회하게 되면 권한을 부여받은 사용자가 부여한 객체 권한 또한 같이 철회되는 종속철회가 발생한다.

```mariadb
REVOKE SELECT ON emp FROM test1;
//test1이 test2에게도 권한을 부여했기 때문에 test2의 권한도 함께 회수된다.
```

<br /><br />

## 역할(Role)

역할이란 사용자에게 부여되는 권한의 그룹을 말한다. 권한을 그룹으로 묶어서 효율적으로 관리할 수 있다. 한 사용자가 여러 개의 역할을 가질 수 있고, 여러 사용자에게 같은 역할을 부여할 수 있다.

<br />

### ORACLE의 기본 역할

```mariadb
CONNECT //create session, create table, create index등 8가지 권한
RESOURCE //create table 등 20가지 권한
DBA //데이터베이스의 모든 권한을 가짐
SYSDBA //데이터베이스 시작과 종료 및 관리에 대한 권한
SYSOPER //SYSDBA 권한과 데이터베이스를 생성할 수 있음
```

<br />

### 역할 생성

```mariadb
CREATE ROLE role_name
	[NOT IDENTIFIED | IDENTIFIED BY password];
```

CREATE ROLE 권한을 가진 USER가 생성할 수 있다.

<br />

### 역할 부여

```mariadb
GRANT role TO role_name; //생성한 role에 CONNECT, RESOURCE 등의 역할을 부여
GRANT role_name TO user_name; //생성한 role을 USER에게 부여
```

<br />

### 역할 철회

```mariadb
REVOKE role FROM role_name; //생성한 role에서 해당 역할을 철회
REVOKE role_name FROM user_name; //해당 USER에게 지정된 role을 철회
```

<br />

```mariadb
CREATE ROLE manage;
GRANT create session, create table TO manage;
GRANT manage TO test1;

CREATE ROLE test;
GRANT connect, resource TO test;
```

<br />

### 역할 변경

```mariadb
ALTER ROLE role_name [NOT IDENTIFIED | IDENTIFIED BY password];
```

<br />

```mariadb
ALTER ROLE manage IDENTIFIED BY commission;
```

<br />

### 역할 설정

```mariadb
SET ROLE [role_name | ALL | NONE] IDENTIFIED BY password;
```

SQL*PLUS 또는 3GL(C, COBOL 등)을 이용하여 역할 부여한다.

```mariadb
ALTER USER user_name
	[DEFAULT ROLE role_name; | ALL EXCEPT role; | NONE;]
```

사용자에게 특정 역할만 부여한다.

