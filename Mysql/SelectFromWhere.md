# 디비 SFW 연습
``` sql
create database ws1;

use ws1;

CREATE TABLE EMP
       (EMPNO int(4)  PRIMARY KEY,
	ENAME VARCHAR(10),
	JOB VARCHAR(9),
	MGR int(4),
	HIREDATE DATE,
	SAL decimal(7,2),
	COMM decimal(7,2),
	DEPTNO int(2));
    
INSERT INTO EMP VALUES
(7369,'SMITH','CLERK',7902,cast('2010-10-19' as date),800,NULL,20);
INSERT INTO EMP VALUES
(7499,'ALLEN','SALESMAN',7698,cast('2000-10-19' as date),1600,300,30);
INSERT INTO EMP VALUES
(7521,'WARD','SALESMAN',7698,cast('2013-05-19' as date),1250,500,30);
INSERT INTO EMP VALUES
(7566,'JONES','MANAGER',7839,cast('2018-10-19' as date),2975,NULL,20);
INSERT INTO EMP VALUES
(7654,'MARTIN','SALESMAN',7698,cast('2008-04-19' as date),1250,1400,30);
INSERT INTO EMP VALUES
(7698,'BLAKE','MANAGER',7839,cast('2016-11-19' as date),2850,NULL,30);
INSERT INTO EMP VALUES
(7782,'CLARK','MANAGER',7839,cast('2017-10-19' as date),2450,NULL,10);
INSERT INTO EMP VALUES
(7788,'SCOTT','ANALYST',7566,cast('2013-10-11' as date),3000,NULL,20);
INSERT INTO EMP VALUES
(7839,'KING','PRESIDENT',NULL,cast('2014-08-19' as date),5000,NULL,10);
INSERT INTO EMP VALUES
(7844,'TURNER','SALESMAN',7698,cast('2010-10-19' as date),1500,0,30);
INSERT INTO EMP VALUES
(7876,'ADAMS','CLERK',7788,cast('2000-09-19' as date),1100,NULL,20);
INSERT INTO EMP VALUES
(7900,'JAMES','CLERK',7698,cast('2018-10-19' as date),950,NULL,30);
INSERT INTO EMP VALUES
(7902,'FORD','ANALYST',7566,cast('2003-10-19' as date),3000,NULL,20);
INSERT INTO EMP VALUES
(7934,'MILLER','CLERK',7782,cast('2002-10-19' as date),1300,NULL,10);

-- 1 테이블 정보확인
desc emp;
-- 2. 이름이 K로 시작하는 사원의 사원번호 이름 입사일 급여 검색
SELECT empno, ename, hiredate, sal
from emp
where ename like "K%";

-- 3. 입사일이 2000년도인 사람으 ㅣ모든 정보
SELECT *
FROM emp
WHERE year(hiredate) = 2000;

-- 4. 커미션이 null이 아닌사람의 모든 정보 검색
SELECT *
FROM emp
WHERE comm is not null;

-- 5.부서가 30번 부서이고 급여가 $1,500 이상인 사람의 이름, 부서 ,월급을 검색하세요
SELECT ename, deptno, sal
FROM emp
WHERE DEPTNO = 30 and sal >= 1500;

-- 6. 부서번호가 30인 사람 중 사원번호 SORT하라.
SELECT *
FROM emp
WHERE deptno = 30
ORDER BY empno asc;

-- 7. 급여가 많은 순으로 SORT하라.
SELECT *
FROM emp
WHERE 1 = 1
ORDER BY sal desc;

-- 8. 부서번호로 ascending sort한 후 급여가 많은 사람 순으로 검색
SELECT *
FROM emp
WHERE 1 =1
ORDER BY deptno asc, sal desc;

-- 9.  부서번호가 DESCENDING SORT하고, 이름 순으로 ASCENDING SORT, 급여 순으로 DESCENDING SORT
SELECT *
FROM emp
WHERE 1 =1
ORDER BY deptno desc, ename asc, sal desc;

-- 10. emp Table에서 이름, 급여, 커미션 금액, 총액(급여+커미션금액)을 
--  구하여 총액이 많은 순서로 검색하세요.. 단, 커미션이 NULL인
--  사람은 제외한다.(커미션금액: sal*comm/100)
SELECT ename, sal, comm , sal+comm/100 AS total
FROM emp
WHERE comm IS NOT NULL
ORDER BY total DESC;

-- 11.   10번 부서의 모든 사람들에게 급여의 13%를 보너스로 지불하기로 하였다. 이름, 급여, 보너스 금액, 부서번호를 검색하세요.
SELECT ename, sal, sal * 0.13 AS bonus, deptno
FROM emp1
WHERE 1 = 1;

-- 12. 부서번호가 20인 사원의 시간당 임금을 계산하여 검색하세요.. 
-- 단, 1달의 근무일수는 12일이고, 1일 근무시간은 5시간이다. 
-- 출력양식은 이름, 급여, 시간당 임금을 검색하세요.

SELECT ename, sal, sal / (12 * 12 * 5) as salperhour
FROM emp
WHERE deptno = 20;

-- 13. 급여가 $1,500부터 $3,000 사이의 사람은 급여의 15%를 회비로 지불하기로 하였다. 이를 이름, 급여, 회비(소수점 두자리아래에서 반올림)를 검색하세요..
SELECT ename, sal, round(sal * 0.15,2) as pay 
FROM emp
WHERE sal between 1500 and 3000

-- 14. 모든 사원의 실수령액을 계산하여 검색하세요.. 단, 급여가 많은  순으로 이름, 급여, 실수령액을 검색하세요..(실수령액은 급여에  대해 10%의 세금을 뺀 금액)
SELECT ename, sal, sal * 0.9 as getsal
FROM emp
WHERE 1 = 1
ORDER BY sal desc;

-- 15.  입사일로부터 오늘까지의 일수를 구하여 이름, 입사일, 근무일수를 검색하세요..
SELECT ename, hiredate, datediff(now(),hiredate) as workdays
FROM emp
WHERE 1 = 1;
```
