# ORACLE



  ## 1. SELECT 와 FROM

``` shell
SELECT * FROM HR.employees;
```

- `*`표시는 <u>'모든 것'</u>을 의미한다.
- 위 구문은 <u>'HR 스키마 안에 들어있는 employees 테이블에서 모든 열의 내용을 가져와라'</u>



 ## 2.  SELECT ~ FROM ~ WHERE

  ###    1)  WHERE 절

``` shell
SELECT * FROM userTBL WHERE userName = '장재영';
```

- `WHERE` 절은 조회하는 결과에 특정한 조건을 줘서, 원하는 데이터만 보고 싶을 때 사용.
- 위 구문은 <u>'장재영'이라는 username을 가진 userTBL에서 모든 열의 내용을 가져와라.</u>

 ###    2) 관계 연산자

``` shell
SELECT userID, userName FROM userTBL WHERE birthYear >= 1970 AND height >= 182;
```

- 위 구문은 <u>'1970년 이후에 출생을 하고 키가 182 이상인 사람의 아이디와 이름을 가져와라'</u>

###  3) BETWEEN ~ AND 와 IN() 그리고 LIKE

``` shell
SELECT userName, height FROM useTBL WHERE height BETWEEN 180 AND 183;
```

- BETWEEN ~AND ~  는 같은 조건 범위.
- 위 구문은  <u>'키가 180~183인 사람의 이름과 키를 가져와라'</u>

``` shell
SELECT userName, height FROM userTBL WHERE userName LIKE '김%';
```

- LIKE는 문자열의 내용 검색.
- 위 구문은  <u>'성이 김씨이고 그 뒤에 무엇이든(%) 허용한다'</u>
- 조건에 '_아%' 앞에는 아무거나 오고 두 번째 글자가 '아'인 문자열 조회.

###   4)  ANY/ALL/SOME 그리고 서브쿼리

``` shell
SELECT rownum, userName, height FROM userTBL WHERE height >= ANY (SELECT height FROM userTBL WHERE addr= '경남');
```

- 서브쿼리가 둘 이상의 값을 반환하면 오류가 나온다. 그래서 `ANY` 구문을 쓰는 것 (=`SOME` 구문)
- `=ANY`구문은 `IN`구문과 동일 (=조건과 동일 값만 출력)
- `ALL`구문은 서브쿼리 여러 개의 결과를 모두 만족 시켜야한다.
- 위 구문은 <u>'경남에 사는 사람들 키보다 같거나 큰 사람의 이름과 키를 가져와라'</u>

###   5) ORDER BY 원하는 순서대로 정렬해서 출력

``` shell
SELECT userName, mDate FROM userTBL ORDER BY mDate;
```

- 오름차순으로 정렬. 내림차순은 열 이름 뒤에 DESC 라고 적기.
- 위 구문은   <u>'가입한 순서대로 정렬해서 오름차순으로 가져와라 '</u>

   ###   6) DISTINCT 중복된 것은 하나만 남기기

``` shell
SELECT DISTINCT addr FROM userTBL;
```

- SELECT 바로 뒤에 붙는 것이 특징
- 위 구문은   <u>'회원들의 거주지역을 가져와라 단 중복되는 것은 없애고'</u>

    ###   7) ROWNUM 열과 SAMPLE 문

``` shell
SELELCT * FROM (SELECT employee_id, hire_date FROM employees ORDER BY hire_date ASC)
WHERE ROWNUM <=5;
```

- 위 구문은   <u>'가입연도가 가장 빨랐던 순서대로 상위 5개만 뽑아줘'</u>
- 임의의 데이터 추출하고 싶다면 `SELECT A FROM B SAMPLE (퍼센트)` 실행 시 마다 다른 데이터 값 반환

###   8) CREATE TABLE ~ AS SELECT 테이블 복사

``` shell
CREATE TABLE buyTBL2 AS (SELECT * FROM buyTBL);
SELECT * FROM buyTBL2;
```

- 위 구문은   <u>'buyTBL의 테이블을 buyTBL2 라는 이름으로 복사해줘'</u>

- AS (SELECT 특정 데이터 FROM buyTBL) 로 특정 데이터만 복사 가능



## 3. GROUP BY  및 HAVING 그리고 집계함수

- ### GROUP BY 절

  - 그룹을 묶어주는 역할

    ```shell
    SELECT userID, SUM(amount) FROM buyTIL GROUP BY userID;
    ```

    - 사용자별로 GROUP하여 묶어준 다음, SUM () 함수로 구매 개수 합친 것
    - 별칭 `AS` = userID AS"사용자 아이디", SUM(amount) AS "총 구매 개수"
    - 구매액의 총합= SUM(amount * price) AS "구매액의 총합" 

- ### 집계 함수

  - AVG() = 평균
  - MIN() = 최소값
  - MAX() = 최대값
  - COUNT() = 행의 개수 세기
  - COUNT(DISTINCT) = 행의 개수 세기 (중복없이)
  - STDEV() = 표준편차
  - VARIANCE() = 분산

- ### Having 절

  - 집계함수는 WHERE 절에 나타날 수 없다

    ```shell
    SELECT userID AS "사용자", SUM(price * amount) AS "총 구매액"
     FROM buyTBL
     GROUP BY userID
     HAVING SUM(price*amount) > 1000;
    ```

  - <B><u>HAVING 절은 꼭 GROUP BY 절 다음 나와야 한다.</u></B>