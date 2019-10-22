# DML(Data Manipulation Language) 

SQL의 데이터 조작어로, 테이블에 데이터를 추가하고 삭제하고 갱신하는 명령어들을 말한다.

<br />

## SELECT

데이터베이스로부터 자료를 검색한다. 사용자는 출력되는 행들의 칼럼을 제한할 수 있고, 그 순서도 명시할 수 있다.

```mariadb
SELECT [DISTINCT] {*, 칼럼 이름 [as 별명]...}
	FROM 테이블 이름
	[WHERE Query 조건]
	[GROUP BY group_by_expression]
	[HAVING group_condition]
	[ORDER BY {칼럼 이름, 표현식} [ASC | DESC]];
```

<br />

### DISTINCT

조회하려는 칼럼의 중복되는 값은 제거 후 출력된다. UNIQUE한 칼럼이나 레코드를 조회할 때 쓰인다. 

DISTINCT 키워드 뒤에 2개 이상의 칼럼을 정의하면 하나의 RECORD로 인식한다.

### WHERE

검색되는 행들을 제한하는 조건문이 쓰인다. 조건문에 아래 연산자들이 사용 가능하다.

**비교 연산자 (=, >, <, !=, <> ...)**

**SQL 연산자(BETWEEN ... AND ..., IN(list), LIKE, IS NULL, NOT)**

**논리 연산자(AND, OR, NOT)**

연산자 우선 순위는 비교 연산자 > AND > OR 순서이다.

### GROUP BY

지정한 칼럼의 데이터를 칼럼 내 다른 데이터들과 비교하여 유일한 값에 따라 무리를 지어준다.

### HAVING

집계 함수를 가지고 조건 비교를 할 때 사용한다.

### ORDER BY

SELECT 명령의 결과로 나온 행들을 정렬할 때 사용한다.

SELECT 절의 마지막에 위치한다.

<br /><br />

### 산술표현식

사칙연산(+, -, *, /)와 괄호 등을 FROM 절을 제외한 SQL문장에 사용 가능하다. 

### ALIAS (별명)

칼럼 혹은 테이블에 별명을 지정하여 출력되는 칼럼과 테이블 이름을 변경할 수 있다.

별명이 공백, 특수문자 포함되거나 대소문자 구분이 필요할 때 인용부호가 필요하고 아닌 경우에는 생략 가능하다.

<br />

```mariadb
SELECT b_no, b_name
	FROM branch
	WHERE b_no > 10;	//b_no가 10보다 큰 경우만 출력

SELECT b_no, b_name, b_assets*0.1 as new_assets
	FROM branch;	//b_assets에 0.1 곱한 값들을 new_assets 칼럼 이름으로 출력

SELECT a_no '계좌번호', nvl(to_char(a_term), '자유저축') '계약기간(월)'
	FROM account;	//a_term이 NULL 값이 아니라면 문자형으로 변환해 출력하고, NULL 값이라면 '자유저축'값으로 출력

SELECT b.dname, COUNT(a.empno) "사원수"
	FROM emp a, dept b
	WHERE a.deptno = b.deptno
	GROUP BY dname
	HAVING COUNT(a.empno) > 5;	//사원수가 5명이 넘는 부서와 사원수를 조회
	
SELECT c_no, c_name, c_dist
	FROM customer
	ORDER BY c_name;	//c_name의 오름차순으로 정렬되어 출력
```

<br />

### 문자 함수

입력으로 문자 데이터를 받아들여 결과값을 문자나 숫자로 돌려준다.

```mariadb
LOWER(칼럼 이름 | 표현식)
//소문자 변환
UPPER(칼럼 이름 | 표현식)
//대문자 변환
INITCAP(칼럼 이름 | 표현식)
//첫글자만 대문자로 변환
CONCAT(칼럼1 이름 | 표현식1, 칼럼2 이름 | 표현식2)
//문자열 합치기
SUBSTR(칼럼 이름 | 표현식, m[, n])
//문자열 자르기
LENGTH(칼럼 이름 | 표현식)
//문자열의 길이 출력
NVL(칼럼1 이름 | 표현식1, 칼럼2 이름 | 표현식2)
//해당 칼럼 값이 NULL이라면 두번째 변수로 지정된 값이 출력
...
```

<br />

### 숫자 함수

```mariadb
ROUND(칼럼 이름 | 표현식, n)
//반올림 함수. n은 소숫점 아래 자리수를 나타냄.
TRUNC(칼럼 이름 | 표현식, n)
//내림 함수. n은 소숫점 아래 자리수를 나타냄.
MOD(m, n)
//m을 n으로 나눈 나머지 반환
ABS(n)
//절댓값 계산
...
```

<br />

### 전환 함수

```mariadb
TO_CHAR(n | date, ['fmt'])
//숫자 또는 날짜 값을 fmt 형식의 문자 스트링으로 전환
TO_NUMBER(char)
//숫자를 포함하고 있는 문자 스트링을 숫자로 전환
TO_DATE(char, ['fmt'])
//날짜를 나타내는 문자 스트링을 명시한 fmt에 따라 날짜 값으로 변환
//fmt의 기본값은 'DD-MM-YY'
```

<br />

### 그룹 함수

```mariadb
AVG
COUNT
MAX
MIN
STDDEV
SUM
VARIANCE
```

`COUNT(*)` 를 제외한 모든 그룹 함수들은 NULL 값을 무시한다.

<br />

```mariadb
SELECT c_no, c_name, c_dist
	FROM customer
	ORDER BY c_name;	//c_name의 오름차순으로 정렬되어 출력

SELECT c_name, length(c_name)
	FROM customer;	//c_name의 글자 수 출력
	
SELECT a_c_no, a_no, sum(a_amount)*0.115	//sum 그룹 함수 이용
	FROM account
	WHERE a_item_dist = 'A0'
	GROUP BY a_no, a_c_no;
	
SELECT MIN(c_name), max(c_name)
	FROM customer; // c_name의 최댓값, c_name의 최솟값 한 행에 출력
	
SELECT a_item_name, max(a_total_amount)
	FROM account
	WHERE a_item_dist='L1'
	GROUP BY a_item_name;
```

<br />

<br />

### 동등 조인

한 테이블에 있는 하나 이상의 칼럼 값이 다른 테이블에 있는 하나 이상의 칼럼 값과 같을 때 선택된다.

#### 1. WHERE절 이용

WHERE절에서 조인 조건으로 `=` 관계연산자를 사용한다.

#### 2. NATURAL JOIN 키워드 이용

양쪽 테이블에 같은 이름과 데이터형을 가지는 칼럼만 조인한다. 공통의 칼럼이 복수 존재할 경우 그것들이 전부 카티전 곱 되어 출력된다. 

모든 칼럼을 대상으로 공통 칼럼을 조사하기 때문에 실행 속도가 느리다.

#### 3. JOIN ~ USING 키워드 이용

두 테이블 간의 조인 조건에 해당하는 공통칼럼명을 기술하여 조인한다.

```mariadb
SELECT c.c_name, a.a_item_name
	FROM customer c, account a
	WHERE c.c_no = a.a_c_no	//WHERE절 이용한 동등 조인
	GROUP BY c.c_name, a.a_item_name;

SELECT ename, dname
	FROM emp NATURAL JOIN dept;	//NATURAL JOIN 이용한 동등 조인

SELECT ename, dname
	FROM emp JOIN dept USING(deptno);	//JOIN ~ USING 이용한 동등 조인
```

<br />

### 외부 조인

조인 조건을 만족하지 못하는 행들을 검색하기 위해 외부 조인을 사용한다.

#### 1. WHERE절의 동등 조인 조건에 `(+)` 추가

데이터가 없을 수도 있는 쪽 JOIN 칼럼에 `(+)` 를 추가한다.

즉, `LEFT OUTER JOIN`일 경우 오른쪽 칼럼에 `(+)` 추가하고, `RIGHT OUTER JOIN`일 경우 왼쪽 칼럼에 `(+)` 추가한다.

#### 2. OUTER JOIN ~ ON ~ 키워드 이용

조건에 따라 `LEFT OUTER JOIN`, `RIGHT OUTER JOIN`, `FULL OUTER JOIN` 을 사용한다. 

<br />

### 셀프 조인

동일 테이블에 조인할 때 사용한다.

#### 1. FROM절에 '테이블명 별명1, 테이블명 별명2' 지정 후 WHERE 절에 '조인 조건'

#### 2. FROM절에 '테이블명 별명1 JOIN 테이블명 별명2 ON 조인 조건'

#### 3. FROM절에 '테이블명 별명1 INNER JOIN 테이블명 별명2 ON 조인 조건'

```mariadb
SELECT a.empno, a.ename
	FROM emp a, emp b
	WHERE a.empno = b.mgr	
	GROUP BY a.empno, a.ename;
	
SELECT a.empno, a.ename
	FROM emp a JOIN emp b ON a.empno = b.mgr
	GROUP BY a.empno, a.ename;
	
SELECT a.empno, a.ename
	FROM emp a INNER JOIN emp b ON a.empno = b.mgr
	GROUP BY a.empno, a.ename;
```

<br />

### 비동등 조인

한 테이블의 어떤 칼럼도 조인할 테이블의 한 칼럼과 직접적으로 일치하지 않을 때 이용한다.

조인 조건에 `=` 조건이 아닌 `<` 관계연산자나 `BETWEEN ~ AND ~` 같은 연산자를 사용한다.

```mariadb
SELECT e.ename, e.job, e.sal, s.grade
	FROM emp e, salgrade s
	WHERE e.sal BETWEEN s.losal AND s.hisal;
	//e.sal 값이 s.losal 보다 크고 s.hisal 보다 작은 범위 안에 있는 s.grade를 출력
```

<br /><br />

### 집합연산자

SELECT 쿼리의 결과를 대상으로 연산을 수행하는 연산자이다.

#### 주의할 점!

각 SELECT 문은 ORDER BY 절을 포함하지 않는다. 그러나 전체 결과 행에 대한 ORDER BY 절은 포함시킬 수 있다.

첫 번째 쿼리 결과의 칼럼 수와 두 번째 쿼리 결과의 칼럼 수가 반드시 같아야 한다.

첫 번째 쿼리 결과와 두 번째 쿼리 결과는 대응되는 칼럼의 데이터 타입과 데이터 명이 반드시 같아야 한다.

큰 데이터 타입(LOB 등)에는 집합연산자 사용이 불가능하다.

#### UNION

첫 번째 쿼리의 모든 행을 두 번째 쿼리의 모든 행과 더하고, 중복된 행을 제거한 뒤 반환한다.

#### INTERSECT

두 쿼리의 결과 셋에 모두 존재하는 행만 결과로 반환한다.

#### MINUS

각 쿼리의 결과 중 첫 번째 쿼리에서만 반환되는 행을 결과로 반환한다.

즉, 첫 번째 쿼리 결과 - 두 번째 쿼리 결과

<br />

```mariadb
SELECT c_name FROM customer WHERE c_dist = '00';
UNION
SELECT c_name FROM customer WHERE c_dist = '11';
//c_dist가 '00'이거나 '11'인 행 출력 (중복된 행은 없음)

SELECT c_name FROM customer WHERE c_addr like '서울%'
INTERSECT
SELECT c_name FROM customer WHERE c_dist = '00';
//c_addr이 '서울'로 시작하고 c_dist가 '00'인 행 출력

SELECT c_name FROM customer
MINUS
SELECT c_name FROM customer WHERE c_dist = '00';
//c_dist가 '00'이 아닌 행 출력
```

<br />

### 서브 쿼리

SQL 문 내에 내포된 SELECT 문을 말한다. 괄호 안에 쿼리를 넣어 처리한다.

서브 쿼리는 알려지지 않은 조건 값에 근거한 값들을 검색하는 질의를 작성하는데 매우 유용하다.

*서브 쿼리에는 ORDER BY 절은 올 수 없다.*

```mariadb
SELECT b_name, b_addr
	FROM branch
	WHERE b_no = 
  	(select b_no
     FROM branch
     WHERE b_assets > 25000000);
```

 서브 쿼리가 먼저 실행되어 자산이 '25000000'보다 큰 지점의 코드가 검색되고, 그 후에 메인 쿼리가 실행된다.

<br />

```mariadb
SELECT DISTINCT a_no, a_c_no, a_total_amount
	FROM account
	WHERE a_item_dist = 'L1'
		AND a_total_amount < 
			(SELECT AVG(a_total_amount) FROM account
      	WHERE a_item_dist = 'L1');
      	
SELECT a_b_no, AVG(a_total_amount)
	FROM account
	WHERE a_item_dist = 'L1'
	GROUP BY a_b_no
	HAVING AVG(a_total_amount) >
		(SELECT AVG(a_total_amount) FROM account
     WHERE a_b_no = '100' and a_item_dist = 'L1');
     
SELECT DISTINCT a.a_no, b.b_name, a.a_b_no
	FROM (SELECT * FROM account) a, branch b
	WHERE a.a_b_no = b.b_no;
	
	
//여러 행이 반환되는 서브 쿼리 "IN" 연산자 사용
SELECT c_name
	FROM customer
	WHERE c_no IN (SELECT DISTINCT(a_c_no) FROM account);
```

