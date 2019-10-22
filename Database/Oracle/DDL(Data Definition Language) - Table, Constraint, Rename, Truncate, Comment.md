# DDL(Data Definition Language) - Table, Constraint, Rename, Truncate, Comment

![OracleLanguageImage.png](https://user-images.githubusercontent.com/16719527/67200364-83544b00-f43e-11e9-8f84-3508d40b5840.png)

## 테이블

### 테이블 생성

```mariadb
CREATE TABLE [schema].table_name
	(column_name datatype [DEFAULT 값][CONSTRAINT 칼럼 레벨의 제약 조건],
	...
	[CONSTRAINT 테이블 레벨의 제약조건]);
```

테이블 이름을 지정하고 테이블의 각 칼럼은 괄호 안에 지정한다. 칼럼 뒤에 데이터 타입은 꼭 지정해야 한다.

각 칼럼들은 쉼표로 구분하고, 구문 마지막은 세미콜론으로 끝나야 한다.

<br />

### 테이블 명명규칙

테이블 이름과 칼럼 이름은 정해진 규칙을 따른다.

1. 이름은 영문자로만 시작하고, 1~30자 길이여야 한다.
2. 문자는 A~Z, a~z, 0~9, _, $, #만 사용할 수 있다.
3. 동일한 사용자가 소유한 다른 객체의 이름과 중복되지 않도록 한다.
4. 예약어는 사용할 수 없다.
5. 테이블과 데이터베이스는 객체를 의미할 수 있는 적절한 이름을 사용한다.

<br />

### 테이블 칼럼 추가

```mariadb
ALTER TABLE table_name
	ADD (column_name datatype [DEFAULT 값][NOT NULL]
  [, column_name datatype ...]);
```

기존의 칼럼은 삭제할 수 없다. 새로운 칼럼은 테이블의 마지막에 추가된다.

<br />

### 테이블 칼럼 수정

```mariadb
ALTER TABLE table_name
	MODIFY (column_name datatype [DEFAULT 값][NOT NULL]
  [, column_name datatype ...]);
```

칼럼에 값이 있으면 크기를 줄일 수 없다.

<br />

### 테이블 삭제

```mariadb
DROP TABLE table_name [CASCADE CONSTRAINT];
```

`CASCADE CONSTRAINT` 옵션을 사용하면 종속 무결성 제약조건까지 삭제한다.

***한번 삭제된 테이블은 롤백할 수 없다.***

<br />

```mariadb
CREATE TABLE employee(
	emp_no VARCHAR2(14),
  emp_name VARCHAR2(14));
  
ALTER TABLE employee
	ADD (e_addr VARCHAR2(20));	//employee 테이블에 e_addr 칼럼 추가

ALTER TABLE employee
	MODIFY (e_addr VARCHAR2(50));	//추가된 e_addr 칼럼의 VARCHAR2 사이즈를 50으로 증가
```

<br /><br />



## 제약 조건

데이터의 무결성 유지를 위해서 사용자가 지정할 수 있는 조건이다. 모든 제약 조건은 데이터 사전에 저장된다.

### PRIMARY KEY

하나의 테이블에서 그 행을 유일하게 식별할 수 있는 칼럼이다. 여러 칼럼을 묶어서 지정할 수도 있다.

*반드시 UNIQUE하고 NOT NULL이다.*

### FOREIGN KEY

칼럼과 참조되는 테이블의 칼럼 간의 참조 관계를 설정하는 제약 조건이다. 테이블과 테이블의 관계를 나타낸다.

참조되는 테이블을 Parent라고 하고, 참조하는 테이블을 Child라고 한다.

FOREIGN KEY이 참조하는 칼럼은 반드시 PRIMARY KEY가 지정된 칼럼이어야 한다.

### NOT NULL

NULL 값이 들어가면 안되는 경우 사용한다. 즉, 반드시 값을 입력해야만 하는 경우.

반드시 칼럼 레벨에서만 정의가 가능하다.

### CHECK

칼럼에 입력할 값이 정해져있는 경우 사용한다. 반드시 참이어야 하는 조건을 명시하는 제약 조건으로 범위 설정에 유효하다.

### UNIQUE

칼럼의 값이 테이블 전체에서 유일한 값이어야하는 경우 사용한다.

### DEFAULT

칼럼에 값을 입력하지 않아도 지정된 값이 입력될 수 있도록 한다.

<br />

### 제약 조건 생성

```mariadb
//칼럼 레벨에서 지정
column [CONSTRAINT 제약 조건 이름] 제약 조건 유형

//테이블 레벨에서 지정
column,
(column, ...),
[CONSTRAINT 제약 조건 이름] 제약 조건 유형
```

<br />

```mariadb
CREATE TABLE customer(
	c_no VARCHAR2(14) PRIMARY KEY,	//PRIMARY KEY 제약 조건
  c_phone VARCHAR2(16) NOT NULL CONSTRAINT st_no_uk UNIQUE,	//NOT NULL 제약 조건과 UNIQUE 제약 조건
  c_gender BOOLEAN CONSTRAINT cus_ge_ck CHECK (e_gender in (T,F)));	//CHECK 제약 조건

CREATE TABLE account(
	a_no CHAR(8),
  a_serial_no DECIMAL(5),
  a_c_no VARCHAR2(14) NOT NULL,
  ...
  CONSTRAINT ac_no_pk PRIMARY KEY(a_no, a_serial_no),	//PRIMARY KEY 제약 조건
  CONSTRAINT a_c_no FOREIGN KEY(a_c_no) REFERENCES customer(c_no));
```

<br />

### 제약 조건 추가

```mariadb
ALTER TABLE table_name
	ADD [CONSTRAINT 제약 조건 이름] type (column_name);
```

제약 조건은 수정할 수 없고 추가와 삭제만 가능하다. 단, NOT NULL은 갱신이 아니라 수정만 가능하다.

<br />

### 제약 조건 삭제

```mariadb
ALTER TABLE table_name
	DROP PRIMARY KEY | UNIQUE (comumn_name) | CONSTRAINT 제약 조건 [CASCADE];
```

<br />

```mariadb
//account 테이블에 a_no 칼럼의 UNIQUE 제약 조건 추가
ALTER TABLE account
	ADD CONSTRAINT a_no_uk UNIQUE(a_no);

//account 테이블에서 PRIMARY 제약 조건 삭제
ALTER TABLE account
	DROP CONSTRAINT PRIMARY KEY CASCADE;
```

<br />

### ENABLE / DISABLE

제약 조건을 사용하게 하거나 사용하지 않게 한다.

```mariadb
ALTER TABLE employee
	ENABLE CONSTRAINT a_no_uk;	//'a_no_uk' 이름의 제약 조건을 사용하게 함

ALTER TABLE employee
	DISABLE CONSTRAINT a_c_no CASCADE;	//'a_c_no' 이름의 제약 조건을 사용하지않게 함
```

<br />

### 제약 조건 관련 Dictionary

```mariadb
SELECT constraint_name, constraint_type, search_condition, r_constraint_name
	FROM user_constraints
	WHERE table_name = 'ACCOUNT';
	
//제약 조건 관련된 칼럼에 대한 Dictionary
SELECT constraint_name, column_name
	FROM user_cons_columns
	WHERE table_name = 'ACCOUNT';
```

<br />

<br />

## RENAME

객체의 이름을 변경하는 명령이다. 테이블, 뷰, 시퀀스, 시노님의 이름을 바꾼다.

```mariadb
RENAME 이전 이름 TO 새로운 이름;

RENAME employee TO emp;	//employee라는 객체의 이름을 emp로 바꿈
```

<br />

## TRUNCATE

테이블의 모든 행을 삭제하고 테이블이 사용한 저장 공간을 앞으로 사용가능하도록 하는 명령이다. 즉, 행을 삭제하고 저장 공간까지 삭제한다.

```mariadb
TRUNCATE TABLE 테이블 이름;

TRUNCATE TABLE emp;	//emp 테이블과 그 저장공간을 삭제함
```

<br />

## COMMENT

테이블에 주석을 추가하는 명령이다.

```mariadb
COMMENT ON TABLE 테이블 이름
	IS '텍스트';
	
COMMENT ON TABLE emp
	IS 'Employee Information';	//emp 테이블에 주석 추가
COMMENT ON TABLE emp
	IS '';	//emp 테이블의 주석 삭제
```

주석의 내용은 Dictionary를 통해 확인할 수 있다.

```mariadb
ALL_COL_COMMENTS
USER_COL_COMMENTS
ALL_TAB_COMMENTS
USER_TAB_COMMENTS
```

