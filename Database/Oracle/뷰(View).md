## 뷰(View)

- 뷰는 데이터베이스의 선택적인 내용을 보여줄 수 있기 때문에 데이터베이스에 대한 엑세스를 제한할 수 있다.

- JOIN 방법을 몰라도 JOIN 한 것 처럼 여러 테이블에 대한 데이터를 뷰를 통해 볼 수 있다. 복잡한 질의를 통해 얻는 결과를 간단한 질의를 사용해 구할 수 있게 한다.

- 한 개의 뷰로 여러 테이블에 대한 데이터를 검색할 수 있다.

- 특정 평가 기준에 따른 사용자 별로 다른 데이터를 액세스 할 수 있다.

<br />

### 뷰 생성

```mariadb
CREATE [OR REPLACE] [FORCE | NOFORCE] VIEW 뷰 이름 [(alias[, alias]) ...]
AS 서브 쿼리
	[WITH CHECK OPTION [CONSTRAINT 제약 조건]]
	[WITH READ ONLY];
```

#### OR REPLACE

뷰를 변경할 수 있다.

#### FORCE

기본 테이블의 유, 무와 관계없이 뷰를 만든다.

#### ALIAS

서브 쿼리를 통해 선택된 값에 대한 칼럼 명을 지정한다.

#### WITH CHECK OPTION

뷰에 의해 액세스 될 수 있는 행만 입력되거나 변경될 수 있음을 지정한다.

제약 조건은 CHECK OPTION 제약 조건에 지정된 이름을 사용한다.

#### WITH READ ONLY

이 뷰에서는 DML이 수행될 수 없게 한다.

<br />

### 

### 뷰의 DML문 사용 규칙

- 단순 뷰에서는 DML 연산을 수행할 수 있다.
- 뷰가 다음 사항을 포함하는 경우 행을 삭제할 수 없다.
  - 그룹 함수
  - JOIN 조건
  - GROUP BY 절
  - DISTINCT 명령
- 뷰가 다음 사항을 포함하는 경우 데이터를 수정할 수 없다.
  - 위 4가지 조건
  - 식으로 정의된 칼럼 (ex. a_assets * 0.1)
- 뷰가 다음 사항을 포함하는 경우 데이터를 추가할 수 없다.
  - 위 5가지 조건
  - 뷰로 선택되지 않은 NOT NULL 칼럼

<br />

### 

### 뷰 삭제

뷰는 데이터베이스의 테이블을 기반으로 하기 때문에, 데이터의 손실없이 뷰 삭제가 가능하다.

```mariadb
DROP VIEW 뷰 이름;
```

<br />



```mariadb
CREATE VIEW account_view
	AS SELECT DISTINCT a_no, a_total_amount, a_item_name FROM account
		WHERE a_item_dist = 'L1' AND a_total_amount BETWEEN 24000000 AND 25000000;
		
desc account_view
>이름		널 		유형
-----------------
a_no	NOT NULL	CHAR(8)
a_total_amount	NUMBER(13)
a_item_name	NOT NULL	VARCHAR2(20)

SELECT * FROM account_view;
```

<br />

```mariadb
CREATE VIEW cust00
	AS SELECT c_no, c_name, c_dist
		FROM customer
		WHERE c_dist = '00'
		WITH CHECK OPTION CONSTRAINT cust00_ck;
		
UPDATE cust00
	SET c_dist = '11'
	WHERE c_no = '990101-1234567';
//에러. 뷰의 WITH CHECK OPTION의 조건에 위배된다.
//WITH CHECK OPTION은 고객 구분이 '11'로 바뀌면 더이상 cust00 뷰를 통해 고객을 검색할 수 없기 때문에 에러가 발생한다.
```

<br />

```mariadb
CREATE VIEW cust11
	AS SELECT c_no, c_name, c_dist
		FROM customer
		WHERE c_dist = '00'
		WITH READ ONLY;
		
DELETE FROM cust11
	WHERE c_no = '990101-1234567';
//에러. WITH READ ONLY 뷰에서는 DML 작업을 수행할 수 없음

DROP VIEW cust11; //'cust11' 뷰 삭제
```

<br />

### 복합 뷰

|            특징            | 단순 뷰 | 복합 뷰  |
| :------------------------: | :-----: | :------: |
|        테이블의 수         |   1개   | 1개 이상 |
| 데이터 그룹 포함 가능 여부 |    O    |    O     |
|       뷰를 통한 DML        |    O    |    X     |

<br />

```mariadb
CREATE OR REPLACE VIEW v_loan_list
	AS SELECT DISTINCT a_no, a_item_name, c_no, c_name
		FROM customer, account
		WHERE customer.c_no = account.a_c_no AND account.a_item_dist = 'L1';
```

