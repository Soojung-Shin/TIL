# DML(Data Manipulation Language) 

SQL의 데이터 조작어로, 테이블에 데이터를 추가하고 삭제하고 갱신하는 명령어들을 말한다.

<br />

## INSERT

테이블에 새로운 행을 추가한다. 한 번에 한 행만 추가된다.

```mariadb
INSERT INTO 테이블 이름 [(칼럼 이름 [, 칼럼 이름 ...])]
VALUES (값 [, 값 ...]);
```

NULL 값을 삽입하는 방법

1. 묵시적인 방법 - null 값을 넣고싶은 칼럼을 제외하고 삽입

2. 명시적인 방법 - VALUES에 null 삽입

<br />

## UPDATE

행의 값을 변경한다.

```mariadb
UPDATE 테이블 이름
	SET 칼럼 이름 = 값 [, 칼럼 이름 = 값 ...]
	[WHERE 조건];
```

*WHERE 절을 생략하면 모든 값이 변경되므로 주의한다.*

<br />

## DELETE

행을 삭제한다.

```mariadb
DELETE FROM 테이블 이름
	[WHERE 조건];
```

FROM 절은 생략 가능하다. *WHERE 절을 생략하면 모든 값이 삭제되므로 주의한다.*

*무결성 제약 조건 에러를 주의해야 한다.*

<br />

```mariadb
CREATE TABLE customer(
  c_no VARCHAR2(14) PRIMARY KEY,
  c_name VARCHAR2(10),
  c_addr VARCHAR2(20),
  c_phone VARCHAR2(15),
  c_di VARCHAR2(2));
  
INSERT INTO customer
	VALUES ('990101-1234567', '신수정', '서울시 노원구', '02-1234-5678', '00');

INSERT INTO customer (c_no, c_name)
	VALUES ('980101-1234567', '홍길동'); //칼럼 명을 선택해서 원하는 칼럼에만 데이터 삽입이 가능
	
UPDATE customer
	SET c_addr = '서울시 중구'
	WHERE c_name = '홍길동';	//c_name이 '홍길동'인 행의 c_addr을 변경
	
DELETE FROM customer
	WHERE c_no = '980101-1234567';
```

