# 간단한 CreateUpdateDelete 실습
``` sql
use scott;

select * from emp where 1 = 1;

-- 1.  emp Table에서 이름, 급여, 커미션 금액, 총액(급여+커미션금액)을 
-- 구하여 총액이 많은 순서로 검색하세요. 단, 커미션이 NULL인
-- 사람은 제외한다.(커미션금액: sal*comm/100)

select ename, sal, comm, sal+comm as total
from emp
where not isnull(comm);

-- 2. 부서번호가 20인 사원의 시간당 임금을 계산하여 검색하세요.
-- 단, 1달의 근무일수는 12일이고, 1일 근무시간은 5시간이다. 
-- 출력양식은 이름, 급여, 시간당 임금을 검색하세요.

select ename, sal, sal / (12 * 5) as salperhour
from emp
where deptno = 20;

SET SQL_SAFE_UPDATES = 0;

drop table book;
create table book (
	isbn char(8) primary key,
    title varchar(50) not null,
    author varchar(10) not null,
    publisher varchar(15) not null,
    price int not null,
    description varchar(200)
);
desc book;
insert into book values('123-1-14','Java와 coffee','diana','자바닷컴',8000,'삶의 즐거움');
insert into book values('555-23-2','AI와 미래','김현주','미래닷컴',20000,null);
insert into book values('123-2-15','Java와 놀기','김태희','자바닷컴',20000,'Java 정복');
insert into book values('123-6-24','Java와 알고리즘','서민규','자바닷컴',20000,'성능 업!!');
insert into book values('423-5-36','IoT 세상','이세준','미래닷컴',25000,null);

-- price 10프로 인하
update book set price = price * 0.9;

-- 이름에 AI포함한 책 20프로 가격 인상
update book set price = price * 1.2
where title like '%AI%';
select * from book where 1 = 1;

-- 도서제목에 Java 포함하고 가격 10000미만인 도서 삭제, 결과 검색
delete from book
where title like "%JAVA%" and price < 10000;
select * from book where 1 = 1;

-- 출판사별 도서 개수와 가격 합계, 가격의 평균 구하기
select publisher, count(*), sum(price), avg(price)
from book
where 1 = 1
group by publisher;
```
