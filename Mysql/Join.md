# 조인 기본 예제
## Join에 on을 이용해서 테이블을 합치는 조건을 쓰고, 이후에 where로 원하는 자료를 찾는것. , ansi 방법을 권장! (아래 bookstoreDB있음)
``` sql
-- join

-- cross join

select * from emp;

select * from dept;

 

select *

from emp, dept;

-- ansi

select * 

from emp cross join dept;

 

-- inner join

select empno, sal, ename, a.deptno, dname

from emp a, dept b

where a.deptno = b.deptno;

-- ansi 

select *

from emp inner join dept on emp.deptno = dept.deptno;

 

select *

from emp inner join dept using (deptno)

-- where ename = "smith";
 

select * from salgrade;

-- 사번 이름 급여 급여등급을 구하세요

select empno, ename, sal , grade

from emp, salgrade

on sal >= losal and sal <= hisal;

 

-- outer join

update emp set deptno = null where empno = 7839;

select * from emp;

 

select *

from emp inner join dept on emp.deptno=dept.deptno;

 

-- 사원의 이름 사번 급여 부서명을 구하세요

-- left outer join

select *

from emp left outer join dept on emp.deptno=dept.deptno;

 

-- right outer join

select *

from emp right outer join dept on emp.deptno=dept.deptno;

 

-- full outer join

-- select *

-- from emp full outer join dept on emp.deptno=dept.deptno;

 

-- natural join

select *

from emp natural join dept;

 

-- self join

select a.ename, a.empno, b.ename,a.sal, b.sal -- 이름 사번 관리자이름, 사원의급여, 관리자급여

from emp a , emp b

on a.mgr = b.empno;

 

-- ansi

select a.ename, a.empno, b.ename, a.sal, b.sal

from emp a left outer join emp b on a.mgr = b.empno;


use bookstore;
select @@sql_safe_updates;
SET SQL_SAFE_UPDATES = 0;


select * from book;
select * from customer;
select * from orders;

select bookname, saleprice,price, name
from orders o
inner join book b on o.bookid = b.bookid
inner join customer c on o.custid = c.custid;

-- SELECT O.*, C.CUST_NAME, P.PRODUCT_NAME
-- FROM TB_ORDER O
-- 	LEFT OUTER JOIN TB_CUST C ON O.CUST_NO = C.CUST_NO AND C.CUST_NAME = 'MIKE'
--	LEFT OUTER JOIN TB_PRODUCT P ON O.PRODUCT_NO = P.PRODUCT_NO;


-- 각 부서별 인원과 합계와 평균을 부서명 과 함께 출력하라

use scott;
select * from emp;
select d.deptno, dname , count(*), count(e.deptno), count(d.deptno), 
	ifnull(sum(sal),0), ifnull(round(avg(sal)),0)
from 
	emp e right join dept d on e.deptno = d.deptno
where e.deptno <> 10
group by d.deptno;


select d.deptno dno, dname , count(*), count(e.deptno) cnt, count(d.deptno), 
	ifnull(sum(sal),0) totsal, ifnull(round(avg(sal)),0) avgsal
from 
	emp e right join dept d on e.deptno = d.deptno
group by d.deptno;


use bookstore;

select o.custid, c.name, o.bookid, b.bookname bname ,o.saleprice, b.price
from orders o
	inner join customer c on o.custid = c.custid
	inner join book b on o.bookid = b.bookid
where b.bookname like "%축구%";

```
# bookstore db
``` sql
-- bookstore 디비
SET SQL_SAFE_UPDATES = 0;
-- bookstore 데이터베이스 구조 내보내기
DROP DATABASE IF EXISTS `bookstore`;
CREATE DATABASE IF NOT EXISTS `bookstore` ;
USE `bookstore`;


-- 테이블 bookstore.book 구조 내보내기
DROP TABLE IF EXISTS `book`;
CREATE TABLE IF NOT EXISTS `book` (
  `bookid` int(11) NOT NULL AUTO_INCREMENT,
  `bookname` varchar(50) NOT NULL,
  `publisher` varchar(50) NOT NULL,
  `price` int(11) NOT NULL,
  PRIMARY KEY (`bookid`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8;

-- 테이블 데이터 bookstore.book:~11 rows (대략적) 내보내기
DELETE FROM `book`;

INSERT INTO `book` (`bookid`, `bookname`, `publisher`, `price`) VALUES
   (1, '축구의 역사', '굿스포츠', 7000),
   (2, '축구 아는 여자', '나무수', 13000),
   (3, '축구의 이해', '대한미디어', 22000),
   (4, '골프 바이블', '대한미디어', 35000),
   (5, '피겨 교본', '굿스포츠', 8000),
   (6, '역도 단계별기술', '굿스포츠', 6000),
   (7, '야구의 추억', '이상미디어', 20000),
   (8, '야구를 부탁해', '이상미디어', 13000),
   (9, '올림픽 이야기', '삼성당', 7500),
   (10, 'Olympic Champions', 'Pearson', 13000);



-- 테이블 bookstore.customer 구조 내보내기
DROP TABLE IF EXISTS `customer`;
CREATE TABLE IF NOT EXISTS `customer` (
  `custid` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `adderss` varchar(100) NOT NULL,
  `phone` varchar(20) NOT NULL,
  PRIMARY KEY (`custid`)
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

-- 테이블 데이터 bookstore.customer:~5 rows (대략적) 내보내기
DELETE FROM `customer`;

INSERT INTO `customer` (`custid`, `name`, `adderss`, `phone`) VALUES
   (1, '박지성', '영국 맨체스타', '010-5000-0001'),
   (2, '김연아', '대한민국 서울', '010-6000-0001'),
   (3, '장미란', '대한민국 강원도', '010-7000-0001'),
   (4, '추신수', '미국 클리블랜드', '010-8000-0001'),
   (5, '박세리', '대한민국 대전', '010-9000-0001');



-- 테이블 bookstore.orders 구조 내보내기
DROP TABLE IF EXISTS `orders`;
CREATE TABLE IF NOT EXISTS `orders` (
  `orderid` int(11) NOT NULL AUTO_INCREMENT,
  `custid` int(11) NOT NULL,
  `bookid` int(11) NOT NULL,
  `saleprice` int(11) NOT NULL,
  `orderdate` date NOT NULL,
  PRIMARY KEY (`orderid`),
  KEY `custid` (`custid`),
  KEY `bookid` (`bookid`),
  CONSTRAINT `bookid` FOREIGN KEY (`bookid`) REFERENCES `book` (`bookid`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `custid` FOREIGN KEY (`custid`) REFERENCES `customer` (`custid`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8;

-- 테이블 데이터 bookstore.orders:~10 rows (대략적) 내보내기
DELETE FROM `orders`;

INSERT INTO `orders` (`orderid`, `custid`, `bookid`, `saleprice`, `orderdate`) VALUES
   (1, 1, 1, 6000, '2016-01-15'),
   (2, 1, 3, 21000, '2016-01-17'),
   (3, 2, 5, 8000, '2016-01-17'),
   (4, 3, 6, 6000, '2016-01-18'),
   (5, 4, 7, 20000, '2016-01-19'),
   (6, 1, 2, 12000, '2016-01-21'),
   (7, 4, 8, 13000, '2016-01-21'),
   (8, 3, 10, 12000, '2016-01-22'),
   (9, 2, 10, 7000, '2016-01-23'),
   (10, 3, 8, 13000, '2016-01-24');

use bookstore;

show tables;

select * from book;
select * from customer;
select * from orders;

```
