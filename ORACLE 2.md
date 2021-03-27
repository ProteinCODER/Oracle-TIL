# 3. GROUP BY  및 HAVING 그리고 집계함수

### ROLLUP(), GROUPING_ID(),CUBE() 함수

- ```SH
  SELECT groupName, SUM(price * amount) AS "비용"
  FROM buyTbl
  GROUP BY ROLLUP (groupName);
  ```

-  총합 또는 중간 합계가 필요하면 GROUP BY절과 함께 ROLLUP 이나 CUBE함수 사용

- 위 구문은 <u>'buyTbl로부터 groupName와 비용이라는 SUM 항목을 추출 후, 각 분류별 합계와 총합계를 구해서 보여줘'</u>

  

- ```
  SELECT prodName, color, SUM(amount) AS "총 합계"
  FROM cubeTbl
  GROUP BY CUBE(color,prodName)
  ORDER BY prodName, color;
  ```

-  다차원 정보 데이터 요약은 CUBE 함수 사용

- 위 구문은 <u>'coubeTbl로부터 prodName과 color , 총합계라는 이름의 amount 총합을 각 색깔별, 제품별 합계와 총합계를 내림차순으로 보여줘'</u>



#  4. WITH절과 CTE

### 비재귀적 CTE

``` shell
WITH abc(userId,total)
AS
(SELECT userId,SUM(pirce * amount))
FROM buyTbl GROUP BY userId
SELECT * FROM abc ORDER BY total DESC ;
```

- 'AS(SELECT ~~)' 에서 조회되는 열과 'WITH abc (~)' 와 개수가 일치해야 한다.

  

``` shell
WITH cte_userTbl(addr,maxHeight)
AS
(SELECT addr,MAX(height) FROM userTbl GROUP BY addr)
SELECT AVG(maxHeight) AS "각 지역별 최고키 평균" FROM cte_userTbl;
```

- 각 지역별 가장 큰 키를 한 명씩 뽑은 후, 그 사람들의 키 평균을 내보시오

- 1단계- 각 지역별 가장 큰 키 뽑는 쿼리 = SELECT addr, MAX(height) FROM userTbl GROUP BY addr

- 2단계- 위 쿼리를 WITH로 묶기= WITH cte_userTbl(addr,maxHeight) AS (SELECT addr, Max(Height) FROM userTbl GROUP BY addr)

- 3단계- 키의 평균을 구하는 쿼리 작성= SELECT AVG(maxHeight) FROM cte_userTbl

- 4단계- 2단계 3단계를 합친다.

  > CTE는 구문이 끝나면 같이 소멸된다 !



### 재귀적 CTE

- 자기 자신을 반복적으로 호출

- ``` shell
  형식: 
  WITH CTE_테이블이름(열이름)
  AS
  (
  쿼리문 1: SELECT * FROM 테이블 A
   UNOIN ALL
  쿼리문 2: SELECT * FROM 테이블 A JOIN CTE_테이블이름
  )
  ```

  

- 회사 조직관계도  예제

- ```
  WITH empCTE(empName, manager, dept, empLevel)
  AS
  (
  (SELECT emp, manager, department, 0
   FROM empTbl
   WHERE manager = '없음')
   
   UNION ALL
  
  (SELECT empTbl.emp, empTbl.manager, empTbl.department,empCTE.empLevel+1
   FROM empTbl INNER JOIN empCTE
     ON empTbl.manager =empCTE.empName)
     )
     SELECT * FROM  empCTE ORDER BY dept, empLevel ;
  ```



# 5. SQL 분류

### 1) DML `(Data Manipulation Language)`

---

- 데이터 조작언어 `<B>SELECT, INSERT, UPDATE, DELETE'</B> 구문

- 트랜잭션 발생하는 SQL도 DML 

  >트랜잭션: 데이터베이스의 상태를 변화시키기 해서 수행하는 작업의 단위를 *뜻*한다. 간단하게 말해서 질의어(*SQL*)를 이용하여 데이터베이스를 접근 하는 것을 의미한다   (출처:https://mommoo.tistory.com/62)



### 2) DDL `Data Definition Language`

---

- 데이터 정의언어 '<B>CEREATE, DROP, ALTER 등</B>' 구문
- 트랜잭션을 발생시키지 않는다. (따라서, 되돌림이나 완전적용을 적용할 수 없다.)



### 3) DCL `Data Control Language`

---

- 데이터 제어언어 <B>'GRANT,REVOKE,DENY 등'</B> 구문

- 사용자에게 어떤 권한을 부여하거나 빼앗을 때 주로 사용하는 구문



### 1) DML 

- ##### INSERT 문

``` shell
INSERT INTO testTbl1 VALUES (값)
```



- ##### 자동으로 증가하는 `SEQUENCE`

  - 시퀀스 생성

  ```shell
  CREATE SEQUENCE idSEQ
    START WITH 1  -- 시작값
    INCREMENT BY 1 ;  -- 증가값
  ```

  - 데이터 입력

  ```SH
  INSERT INTO testTbl1 VALUES (idSEQ.NEXTVAL,'재영',25,DEFAULT);
  INSERT INTO testTbl1 VALUES (idSEQ.NEXTVAL,'재순',41,'미국');
  ```

  ``` shell
  INSERT INTO testTBL1 VALUES (11,'재혁',22,DEFUALT);
  ALTER SEQUENCE idSEQ 
   INCREMENT BY 10; -- 증가값을 다시 설정. 현재 자동으로 2까지 만들어졌기때문에 10을 더해서 12로 재혁이가 생성됨.
  
  INSERT INTO testTBL1 VALES (idSEQ.NEXTVAL, 'LK',24,'인도');
  ALTER SEQUENCE idSEQ
   INCREMENT BY 1; -- 다시 증가값을 1로 설정. 12까지 만들어졌기 때문에 1씩 증가해서 13으로 LK가 생성됨.
  ```



- ##### 대량의 샘플 데이터 생성

  ``` shell
  INSER INTO 테이블이름 (열이름1, 열이름2 ~)
   SELECT문 ;
  ```

  - SELECT 문의 결과 열의 개수 는 INSERT 할 테이블의 열 개수와 일치해야 한다.

    

- #####  데이터 수정 

  ``` SHELL
  UPDATE 테이블이름
   SET 열1= 값1, 열2= 값2 ~
   WHERE 조건;     --타깃
  ```
  - WHERE 절 생략시, 테이블 전체의 행이 변경된다.

    

- #####  데이터 삭제

  ``` shell
  DELETE FROM 테이블 이름  WHERE 조건 AND ROWNUM <=2;
  ```

  - 위 구문은 조건에 해당되는 데이터가 상위 2개만 삭제가 된다.

  - 대용량 데이터 삭제는 DROP, TRUNCATE로 삭제하는 것이 효율적

    

- ##### 조건부 데이터 변경: MERGE

  - 기존 테이블에서 아이디 이름 주소만 가져와서 따로 생성

    ``` shell
    CREATE TABLE memberTBL AS
     (SELECT userID, userName, addr FROM userTbl) ;
     SELECT * FROM memberTBL ;   -- CREATE한 memberTBL 을 불러줘~
    ```

    ---

    ###### <MERGE 예제>

  ``` shell
  MERGE INTO memberTBL M        --target 테이블 (변경될 테이블)
   USING (SELECT changeType, userID, userName, addr FROM changeTBL) C
   ON (M.userID= C.userID)      --userID를 기준으로 두 테이블 비교    // 변경할 기준이 되는 테이블 (source 테이블)
   
   WHEN MACHED THEN
    UPDATE SET M.addr = C.addr          -- target 테이블에 source 테이블의 행이 있으면 주소를 변경한다.
    DELETE WHERE C.changeType ='회원탈퇴' -- target 테이블에 source 테이블의 행이 있고 사유가 `회원탈퇴`라면 해당 행을 삭제한다.
   
   WHEN NOT MATCHED THEN     -- target 텡블에 source 테이블의 행이 없으면 새로운 행을 추가한다.
    INSERT (userID, userName, addr) VALUES(C.userID, C.userName, C.addr) ;
  ```

  



