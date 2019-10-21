# DDL(Data Definition Language)

데이터베이스 관리자가 테이블 스페이스를 관리하고 사용자 계정 정보를 관리하는 명령어들을 말한다. 또한 사용자가 테이블과 같은 데이터 구조를 정의하는데 사용되는 명령어들로 생성, 변경, 삭제, 이름 변경 등 데이터 구조와 관련된 명령어들을 말한다. CREATE, ALTER, DROP, RENAME, TRUNCATE 등이 있다.

<br />

## 테이블 스페이스

데이터베이스에서 생성하는 Table, Index, View, Synonym과 같은 객체를 저장할 수 있는 공간이다.

개발하는 업무에 따라 여러 개를 생성할 수 있다.

하나의 테이블스페이스는 최소한 하나의 물리적 데이터 파일로 구성된다.

<br />

### 테이블 스페이스 생성

```mariadb
CREATE TABLESPACE tablespace_name
	DATAFILE file_definition [, file_definition]...
	//새로 생성하는 테이블 스페이스가 사용할 데이터 파일 지정
	
	[AUTOEXTEND file_definition [file_definition]] [ON | OFF]
  	[NEXT integer [K | M]]
  	[MAXSIZE [UNLIMITED | integer [K | M]]]
		//생성한 데이터 파일이 다 채워졌을 경우 자동으로 데이터 파일을 확장하기위해 사용하는 옵션

	[DEFAULT STORAGE storage]
	//테이블 스페이스에 생성되는 모든 객체에 대한 스토리지 매개변수를 지정
	
	[ONLINE | OFFLINE];
	//ONLINE : 새로 생성되는 테이블 스페이스를 활성화시키며, 생성 후 바로 사용할 수 있게 함
	//OFFLINE : 테이블 스페이스를 비활성시키며, 생성 후 바로 사용할 수 없음
```

<br />

```mariadb
CREATE TABLESPACE firstbank
	DATAFILE 'C:\app\soojung\oradata\orcl\first01.dbf' SIZE 10M;
```

firstbank라는 테이블 스페이스를 생성하고 'first01.dbf'라는 데이터 파일을 사용한다. 해당 데이터 파일의 사이즈는 10M으로 지정한다.

<br />

### 데이터 파일 추가

```mariadb
ALTER TABLESPACE tablespace_name
	ADD DATAFILE '경로\파일명' SIZE integer [M | K];
```

<br />

```mariadb
ALTER TABLESPACE firstbank
	ADD DATAFILE 'C:\app\soojung\oradata\orcl\first02.dbf' SIZE 1M;
```

firstbank라는 테이블 스페이스에 'first02.dbf'라는 데이터 파일을 추가하고, 해당 데이터 파일의 사이즈는 1M로 제한한다.

<br />

### 데이터 파일 크기 변경

```mariadb
ALTER DATABASE
	DATAFILE '경로\파일명' RESIZE integer [M | K];
```

<br />

```mariadb
ALTER DATABASE
	DATAFILE 'C:\app\soojung\oradata\orcl\first01.dbf' RESIZE 30M;
```

'first01.dbf'라는 데이터 파일의 사이즈를 30M로 변경한다.

<br />

### 데이터 파일 크기 자동화

```mariadb
ALTER DATABASE
	DATAFILE '경로\파일명'
	AUTOEXTEND ON					//데이터 파일의 크기를 자동으로 증가시킨다.
	NEXT integer [M | K]	//한 번에 NEXT 옵션으로 지정한 크기 만큼 증가
	MAXSIZE integer [M | K]; //MAXSIZE 옵션으로 지정한 크기가 될 때까지 증가한다.
```

<br />

```mariadb
ALTER DATABASE
	DATAFILE 'C:\app\soojung\oradata\orcl\first01.dbf'
	AUTOEXTEND ON
	NEXT 1M
	MAXSIZE 10M;
```

데이터 파일이 가득 차면 1M씩 10M가 될 때까지 증가한다. 

<br />

### 테이블 스페이스 삭제

```mariadb
DROP TABLESPACE tablespace_name INCLUDING CONTENTS [AND DATAFILES];
```

<br />

```mariadb
DROP TABLESPACE firstbank INCLUDING CONTENTS;
EXIT;
c:\>cd c:\app\soojung\oradata\orcl
c:\app\soojung\oradata\orcl>del first01.dbf
--> firstbank 테이블스페이스의 데이터  파일을 삭제해야 합니다. 

DROP TABLESPACE firstbank INCLUDING CONTENTS AND DATAFILES;
//테이블 스페이스와 데이터 파일이 한 번에 삭제된다.
```

<br />

<br />

## 사용자 계정

오라클 데이터베이스는 모든 Table, Index, View 등과 같은 객체를 사용자 단위로 생성하고 관리한다.  오라클 데이터베이스를 설치하면 SYS, SYSTEM과 같은 사용자(관리자)가 기본적으로 생성되며 각 사용자는 자신이 생성한 객체들을 관리한다. 오라클 데이터베이스 관리자는 추가로 사용자를 생성할 수 있으며 사용자를 생성할 때는 해당 업무에 적합한 이름을 사용하는 것이 좋다.

<br />

### 사용자 생성

```mariadb
CREATE USER user_name
	IDENTIFIED [BY password | EXTERNALLY] //해당 user에 접근시 사용할 비밀번호 지정
	//IDENTIFIED EXTERNALLY로 지정하면 윈도우 인증으로 접속

	[DEFAULT TABLESPACE tablespace_name]
	//사용자 schema를 위한 기본 테이블 스페이스 지정
	[TEMPORARY TABLESPACE tablespace_name]
	//사용자의 임시 테이블 스페이스 지정
	
	[QUOTA [integer [K | M] | UNLIMITED] ON tablespace_name]
	//해당 사용자가 사용할 테이블 스페이스의 영역을 지정
	//테이블 스페이스의 사용 용량을 한정함
	
	[PROFILE profile_name];
```

<br />

```mariadb
CREATE USER test1 IDENTIFIED BY "1234";
CREATE USER test2 IDENTIFIED BY a1b2c3; //문자가 맨 앞에 나올 시 따옴표 사용하지않음
```

<br />

### 사용자 삭제

```mariadb
DROP USER user_name CASCADE;
```

CASCADE 옵션은 해당 USER가 객체를 가지고 있다면 꼭 사용해야 한다. 해당 USER와 관련된 모든 데이터베이스 스키마가 삭제되고 물리적으로도 삭제된다.
