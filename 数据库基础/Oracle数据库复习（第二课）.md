##1、自连接（相同的两个表进行连接）
```sql
--查询某个员工的上司
select  (a.ename || ' boss is  ' || b.ename) relationship from emp a, emp b where a.mgr = b.empno;
```
```sql
RELATIONSHIP
------------------------------
FORD boss is  JONES
SCOTT boss is  JONES
TURNER boss is	BLAKE
ALLEN boss is  BLAKE
WARD boss is  BLAKE
JAMES boss is  BLAKE
MARTIN boss is	BLAKE
MILLER boss is	CLARK
ADAMS boss is  SCOTT
BLAKE boss is  KING
JONES boss is  KING
CLARK boss is  KING
SMITH boss is  FORD
```
```sql
---我们发现大老板没有上级，这个时候需要使用外连接
select  (a.ename || ' boss is  ' || nvl(b.ename,'himself')) relationship from 
emp a, emp b where a.mgr = b.empno(+);
--“+”号一边是不满足的一边
```
```sql
RELATIONSHIP
------------------------------
FORD boss is  JONES
SCOTT boss is  JONES
JAMES boss is  BLAKE
TURNER boss is	BLAKE
MARTIN boss is	BLAKE
WARD boss is  BLAKE
ALLEN boss is  BLAKE
MILLER boss is	CLARK
ADAMS boss is  SCOTT
CLARK boss is  KING
BLAKE boss is  KING
JONES boss is  KING
SMITH boss is  FORD
KING boss is  himself
```
##2、子查询
一般子查询：子查询把查询结果返回给主查询使用。  
相关子查询：主查询把select列中的参数传递给子查询（注意：这个子查询只能返回一行*** 如果返回多行就会出现不是一一对应的情况，就会出错。）  
子查询可以放置位置：  from 子查询 where子查询 having 子查询 select 后子查询  
order by 不可以  group by 不可以

返回单行的子查询称之为单行子查询，单行子查询只能使用 < > <= >= = 等单行比较操作符  
返回多行的子查询分为多行子查询 ,可以使用 in （或逻辑）， any（任意）  all（所有） (how to use ???)

   eg：一般子查询
  
```sql
--查询部门为“SALES”的员工信息
 select e.* from dept d, emp e where d.deptno = e.deptno and d.dname = 'SALES';

     EMPNO ENAME      JOB	       MGR HIREDATE	    SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
      7521 WARD       SALESMAN	      7698 22-FEB-81	   1250        500	   30
      7844 TURNER     SALESMAN	      7698 08-SEP-81	   1500 	   0	   30
      7499 ALLEN      SALESMAN	      7698 20-FEB-81	   1600        300	   30
      7900 JAMES      CLERK	          7698 03-DEC-81       950 		           30
      7698 BLAKE      MANAGER	      7839 01-MAY-81	   2850 		       30
      7654 MARTIN     SALESMAN	      7698 28-SEP-81	   1250       1400	   30


select e.* from emp e where e.deptno = (select deptno from dept where dname = 'SALES');

     EMPNO ENAME      JOB	       MGR HIREDATE	    SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
      7499 ALLEN      SALESMAN	      7698 20-FEB-81	   1600        300	   30
      7521 WARD       SALESMAN	      7698 22-FEB-81	   1250        500	   30
      7654 MARTIN     SALESMAN	      7698 28-SEP-81	   1250       1400	   30
      7698 BLAKE      MANAGER	      7839 01-MAY-81	   2850 		       30
      7844 TURNER     SALESMAN	      7698 08-SEP-81	   1500 	   0	   30
      7900 JAMES      CLERK	          7698 03-DEC-81	    950 		       30

--查询和员工编号为7564一个工种 并且工资比7521工资高的员工信息
select * from emp a where  a.job = (select  b.job from emp b where  b.empno =
 7654) and a.sal > (select c.sal from emp c where c.empno = 7521);

 EMPNO ENAME      JOB	         MGR   HIREDATE      SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
 7499 ALLEN      SALESMAN       7698  20-FEB-81     1600       300     30
 7844 TURNER     SALESMAN       7698  08-SEP-81     1500               30
 
-查询工资最少的员工的信息
select * from  emp where sal = (select min(sal) from emp);

     EMPNO ENAME      JOB	       MGR HIREDATE	    SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
      7369 SMITH      CLERK	      7902 17-DEC-80	    800 		   20
 
--求比30号部门的最低工资高的其他部门的最低工资信息信息
select deptno , min(sal)from emp group by deptno having min(sal) >(select 
min(sal) from emp where deptno = 30);  
    DEPTNO   MIN(SAL)
---------- ----------
	   10	 1300
--查询比30号部门所有员工薪资高的员工信息
select * from emp where sal >all(select sal from emp where empno = 30);
--查询比30号部门任意一个员工薪资高的员工信息
select * from emp where sal >any(select sal from emp where empno = 30);
``` 
##3、面试强化：子查询
```sql
--不是经理的员工信息(做滤空修正，使得空值不影响结果)
select * from emp where empno not in (select nvl(mgr,-1) from emp);

select * from emp where empno not in (select mgr from emp where mgr
 is not null);  
--找员工表中工资最高的前三名(隐藏列rownum)
select * from (select * from emp order by sal desc) where rownum <=3 ;
     EMPNO ENAME      JOB	       MGR HIREDATE	    SAL       COMM     DEPTNO
---------- ---------- --------- ---------- --------- ---------- ---------- ----------
     7839 KING       PRESIDENT 	           17-NOV-81      5000         10
     7788 SCOTT      ANALYST	   7566    19-APR-87      3000         20
     7902 FORD       ANALYST	   7566    03-DEC-81	  3000 		   20
```

关于rownum的说明，当生成查询结果之后，会对查询结果自动增加rownum。 并且rownum 只能使用<=, <
而没有> ，因为结果集的生成是一行一行的，所以只能从第一行开始。依次增大，否则没办法匹配到之后的行。

```sql
select rownum , e.empno, e.ename, e.job, e.hiredate from emp e order by sal desc;

    ROWNUM	EMPNO ENAME	 JOB	   HIREDATE
---------- ---------- ---------- --------- ---------
	 9	 7839 KING	 PRESIDENT 17-NOV-81
	13	 7902 FORD	 ANALYST   03-DEC-81
	 8	 7788 SCOTT	 ANALYST   19-APR-87
	 4	 7566 JONES	 MANAGER   02-APR-81
	 6	 7698 BLAKE	 MANAGER   01-MAY-81
	 7	 7782 CLARK	 MANAGER   09-JUN-81
	 2	 7499 ALLEN	 SALESMAN  20-FEB-81
	10	 7844 TURNER	 SALESMAN  08-SEP-81
	14	 7934 MILLER	 CLERK	   23-JAN-82
	 3	 7521 WARD	 SALESMAN  22-FEB-81
	 5	 7654 MARTIN	 SALESMAN  28-SEP-81
	11	 7876 ADAMS	 CLERK	   23-MAY-87
	12	 7900 JAMES	 CLERK	   03-DEC-81
	 1	 7369 SMITH	 CLERK	   17-DEC-80
--通过上面的结果我们可以看出，当系统进行查询之后会得到数据集，之后对数据集进行处理之后得到我们
--所需要的结果集，这个数据集会自带rownum属性，当我们进行排序之后，这个rownum也会随之进行排序。
--因此我们要像获得想要的结果，应该使用子查询。获取rownum <=3的列就可以了。


```
##4、面试强化：分页问题
```sql
---获取工资第三到第七的员工信息
select b.empno, b.ename, b.sal, b.deptno from (select rownum r, a.* from (select  
* from emp  order by sal desc  )  a )b where r >=3 and r <=7;

     EMPNO ENAME	     SAL     DEPTNO
---------- ---------- ---------- ----------
      7788 SCOTT	    3000	 20
      7566 JONES	    2975	 20
      7698 BLAKE	    2850	 30
      7782 CLARK	    2450	 10
      7499 ALLEN	    1600	 30
```

##5、面试强化
```sql 
---找到员工表中薪水大于本部门平均薪水的员工
--方法1
select e.empno, e.deptno, e.ename, e.sal , avgsal from emp e ,(select deptno , 
avg(sal) avgsal from emp group by deptno )  d where e.sal > d.avgsal and e.deptno = d.deptno;
 EMPNO     DEPTNO ENAME		SAL	AVGSAL
---------- ---------- ---------- ---------- ----------
      7499	   30 ALLEN	       1600 1566.66667
      7566	   20 JONES	       2975	  2175
      7698	   30 BLAKE	       2850 1566.66667
      7788	   20 SCOTT	       3000	  2175
      7839	   10 KING	       5000 2916.66667
      7902	   20 FORD	       3000	  2175

--方法2
select e.empno, e.ename, e.sal, (select avg(sal) from emp where e.deptno = deptno)
  avgsal  from emp e
where sal > (select avg(sal) from emp where e.deptno = deptno) ;
  EMPNO ENAME	     SAL     AVGSAL
---------- ---------- ---------- ----------
      7499 ALLEN	    1600 1566.66667
      7566 JONES	    2975       2175
      7698 BLAKE	    2850 1566.66667
      7788 SCOTT	    3000       2175
      7839 KING 	    5000 2916.66667
      7902 FORD 	    3000       2175
 --主查询通过别名把主查询的值传递给子查询（****）
``` 
##6、集合运算

```sql
union ---重复部分只有一份
union all ---重复部分有两份
intersect --交集
minus ---差集 A- B 保留A独有部分
```
注意参与运算的集合的列数和对应列的数据类型是一致的

##7、数据处理: insert
```sql
--DML: 数据库操作语言 对表增删改差
--DDL:数据库定义语言 创建表、视图、索引、序列
--DCL: 提交 回滚 savepoint
```
```sql
--创建表
create table empyyc as select * from emp  where 1 = 2;
Table created.

--批量插入数据
insert into empyyc  select *from emp where deptno = 30;
6 rows created.

select empno, ename, sal, deptno from empyyc;

     EMPNO ENAME	     SAL     DEPTNO
---------- ---------- ---------- ----------
      7499 ALLEN	    1600	 30
      7521 WARD 	    1250	 30
      7654 MARTIN	    1250	 30
      7698 BLAKE	    2850	 30
      7844 TURNER	    1500	 30
      7900 JAMES	     950	 30
--向表中插入所有列
insert into empyyc values(9508, 'yyc', 'clerk', 7499, sysdate, 10000,500, 10);
1 row created.

     EMPNO ENAME	     SAL     DEPTNO
---------- ---------- ---------- ----------
      7499 ALLEN	    1600	 30
      7521 WARD 	    1250	 30
      7654 MARTIN	    1250	 30
      7698 BLAKE	    2850	 30
      7844 TURNER	    1500	 30
      7900 JAMES	     950	 30
      9508 yyc		   10000	 10
--向表中插入部分列
insert into empyyc(empno,ename,sal,comm, deptno) values(9509, 'wxj',10001, 501,30);
1 row created.

     EMPNO ENAME	     SAL     DEPTNO
---------- ---------- ---------- ----------
      7499 ALLEN	    1600	 30
      7521 WARD 	    1250	 30
      7654 MARTIN	    1250	 30
      7698 BLAKE	    2850	 30
      7844 TURNER	    1500	 30
      7900 JAMES	     950	 30
      9508 yyc		   10000	 10
      9509 wxj		   10001	 30
--隐式插入空值
--默认情况下，没有显示插入值的列为null
--sql插入地址符 & 取地址符相当于一个变量
```
##8、update、delete

```sql
update  emmyyc set sal = 1000 where ename = 'wxj';
delete [from] empyyc where empno = 9509;
```
##9、delete 与truncate的比较

```sql
1 delete 是逐条删除、truncate 是先摧毁表再重建
2 delete 是dml语言 truncate是ddl语言 dml语言可以闪回，做错了并且提交了，
可以通过闪回，撤销操作。 ddl不支持闪回。（两次提交原理 ：需要开启行移动功能才能闪回）
3 delete是逐条删除，会产生碎片， truncate不会产生碎片。
4 delete不会释放空间 truncate会释放空间
5 delete 可以回滚 truncate不可以
6 oracle delete快 mysql truncate 快
```

##10、事物及其相关概念
###基本概念
事物是数据库管理系统执行过程中的一个逻辑单位，由一个有限的数据库操作序列构成（一个或者多个dml语句组成）。事物具有要么全部成功，要么全部失败的特性。具有ACID的特性。  

原子性（Atomicity）：事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行。   
一致性（Consistency）：事务应确保数据库的状态从一个一致状态转变为另一个一致状态，一致状态的含义是数据库中的数据应满足完整性约束。  
隔离性（Isolation）：多个事务并发执行时，一个事务的执行不应影响其他事务的执行。  
持久性（Durability）：已被提交的事务对数据库的修改应该永久保存在数据库中。    
###oracle中的事物
1、事物开始的标志：

```sql
dml语句，因为oracle数据库的事物默认是打开的。 
``` 
2、事物的结束标志：

```sql
1、显式提交 commit；
2、执行ddl语句，如果执行ddl语句之前有dml语句（insert， update 等等 ***）会导致事物的提交。    
3、显示回滚 rollback。 隐式回滚 掉电、宕机、非正常退出、系统出错等等。
```
注：对信息系统来说，在应用程序启动之前，会完成所有表的创建以及初始化，不会在应用程序执行过程中
创建表，以防出现事物问题。

3、关于事物隔离性的讨论。

```sql
oracle sqlserver的默认隔离级别是读已提交，mysql的默认隔离级别是可重复读。
```
###事物的四个隔离级别（对同一个事物的先后两次操作的影响）

-  Read UnCommitted(读未提交)  
	最低的隔离级别。一个事务可以读取另一个事务并未提交的更新结果。

-  Read Committed(读提交)

	大部分数据库采用的默认隔离级别。一个事务的更新操作结果只有在该事务提交之后，另一个事务才可以的读取到同一笔数据更新后的结果。

-  Repeatable Read(重复读)
	mysql的默认级别。整个事务过程中，对同一笔数据的读取结果是相同的，不管其他事务是否在对共享数据进行更新，也不管更新提交与否。

-  Serializable(序列化)  
	最高隔离级别。所有事务操作依次顺序执行。注意这会导致并发度下降，性能最差。通常会用其他并发级别加上相应的并发锁机制来取代它。

###不同的隔离级别导致的问题
-  脏读
	脏读发生在一个事务A读取了被另一个事务B修改，但是还未提交的数据。假如B回退，则事务A读取的是无效的数据。这跟不可重复读类似，但是第二个事务不需要执行提交。 

-  不可重复读  
	在基于锁的并行控制方法中，如果在执行select时不添加读锁，就会发生不可重复读问题。
在多版本并行控制机制中，当一个遇到提交冲突的事务需要回退但却被释放时，会发生不可重复读问题。

	在上面这个例子中，事务2提交成功，它所做的修改已经可见。然而，事务1已经读取了一个其它的值。在序列化和可重复读的隔离级别中，数据库管理系统会返回旧值，即在被事务2修改之前的值。在提交读和未提交读隔离级别下，可能会返回被更新的值，这就是“不可重复读”。
 
	有两个策略可以防止这个问题的发生：  
(1) 推迟事务2的执行，直至事务1提交或者回退。这种策略在使用锁时应用。  
(2) 而在多版本并行控制中，事务2可以被先提交。而事务1，继续执行在旧版本的数据上。当事务1终于尝
试提交时，数据库会检验它的结果是否和事务1、事务2顺序执行时一样。如果是，则事务1提交成功。如果不是，事务1会被回退。  
 
- 幻读   
幻读发生在当两个完全相同的查询执行时，第二次查询所返回的结果集跟第一个查询不相同。
发生的情况：没有范围锁。

###不可重复读和幻读的比较
-  不可重复读     
	不可重复读的重点是<font color="red">修改</font>: 同样的条件, 你读取过的数据, 再次读取出来发现值不一样了

例子:  
在事务1中，Mary 读取了自己的工资为1000,操作并没有完成 

```sql
con1 = getConnection();  
select salary from employee empId ="Mary"; 
``` 
在事务2中，这时财务人员修改了Mary的工资为2000,并提交了事务. 

```sql
con2 = getConnection();  
update employee set salary = 2000;  
con2.commit(); 
```

在事务1中，Mary 再次读取自己的工资时，工资变为了2000 
```sql
select salary from employee empId ="Mary"; 
```
在一个事务中前后两次读取的结果并不致，导致了不可重复读。

 -  幻读
幻读的重点在于<font color="red">新增或者删除 (数据条数变化)</font>.同样的条件, 第1次和第2次读出来的记录数不一样  
例子：    
目前工资为1000的员工有10人。 
事务1,读取所有工资为1000的员工。  
   
```sql
con1 = getConnection();  
Select * from employee where salary =1000;  
```
共读取10条记录 

这时另一个事务向employee表插入了一条员工记录，工资也为1000 

```sql
con2 = getConnection();  
Insert into employee(empId,salary) values("Lili",1000);  
con2.commit();  
```
事务1再次读取所有工资为1000的员工 

```sql
select * from employee where salary =1000; 
``` 
共读取到了11条记录，这就像产生了幻读。 


###对读已提交隔离级别的验证
```sql
	--原始数据
insert into empyyc select *from emp where deptno = 30;
commit;
select ename , job, mgr, hiredate, sal, deptno from empyyc;

ENAME	   JOB		    MGR HIREDATE	 SAL	 DEPTNO
---------- --------- ---------- --------- ---------- ----------
ALLEN	   SALESMAN	   7698 20-FEB-81	1600	     30
WARD	   SALESMAN	   7698 22-FEB-81	1250	     30
MARTIN	   SALESMAN	   7698 28-SEP-81	1250	     30
BLAKE	   MANAGER	   7839 01-MAY-81	2850	     30
TURNER	   SALESMAN	   7698 08-SEP-81	1500	     30
JAMES	   CLERK	   7698 03-DEC-81	 950         30

--可重复读实验
开启两个连接，一个执行insert语句，另外一个执行查找操作。
link1: 
 insert into empyyc values(9509, 'yyc', 'clerk', 7499, sysdate, 10000,500, 10);

1 row created.

select ename , job, mgr, hiredate, sal, deptno from empyyc;

ENAME	   JOB		    MGR HIREDATE	 SAL	 DEPTNO
---------- --------- ---------- --------- ---------- ----------
ALLEN	   SALESMAN	   7698 20-FEB-81	1600	     30
WARD	   SALESMAN	   7698 22-FEB-81	1250	     30
MARTIN	   SALESMAN	   7698 28-SEP-81	1250	     30
BLAKE	   MANAGER	   7839 01-MAY-81	2850	     30
TURNER	   SALESMAN	   7698 08-SEP-81	1500	     30
JAMES	   CLERK	   7698 03-DEC-81	 950	     30
yyc	   clerk	   7499 06-OCT-19      10000	     10

7 rows selected.


link2:

select ename , job, mgr, hiredate, sal, deptno from empyyc;

ENAME	   JOB		    MGR HIREDATE	 SAL	 DEPTNO
---------- --------- ---------- --------- ---------- ----------
ALLEN	   SALESMAN	   7698 20-FEB-81	1600	     30
WARD	   SALESMAN	   7698 22-FEB-81	1250	     30
MARTIN	   SALESMAN	   7698 28-SEP-81	1250	     30
BLAKE	   MANAGER	   7839 01-MAY-81	2850	     30
TURNER	   SALESMAN	   7698 08-SEP-81	1500	     30
JAMES	   CLERK	   7698 03-DEC-81	 950         30

数据没有变化

link1:
commit;
link2:
select ename , job, mgr, hiredate, sal, deptno from empyyc;
ENAME	   JOB		    MGR HIREDATE	 SAL	 DEPTNO
---------- --------- ---------- --------- ---------- ----------
ALLEN	   SALESMAN	   7698 20-FEB-81	1600	     30
WARD	   SALESMAN	   7698 22-FEB-81	1250	     30
MARTIN	   SALESMAN	   7698 28-SEP-81	1250	     30
BLAKE	   MANAGER	   7839 01-MAY-81	2850	     30
TURNER	   SALESMAN	   7698 08-SEP-81	1500	     30
JAMES	   CLERK	   7698 03-DEC-81	 950	     30
yyc	   clerk	   7499 06-OCT-19      10000	     10

7 rows selected.

---可重复读实验
---可重复读指的是在同一个事物中的前后两次读取，值是不变的。
--不可重复读指的是事物1先后两次执行同一查找结果不一致。导致这个现象出现的原因是因为
--其他事物在事物1执行过程中对事物1的操作的数据进行了修改。
```

##11、保存点
通过设置保存点，可以回退到指定位置。

```sql
savepoint a;
insert into empyyc values(9509, 'yyc', 'clerk', 7499, sysdate, 10000,500, 10);
savepoint b;
insert into empyyc values(9510, 'wxj', 'clerk', 7499, sysdate, 10000,500, 10);
rollback to savepoint a;
--事物a位置还在，但是b不在。
```




##12、创建和管理表


```sql
--增加列 
 alter table empyyc add firstname varchar2(40);
 
--删除列
alter table empyyc drop column firstname;

--重命名列
alter table empyyc rename column firstname to 1stname;

--修改数据类型长度
alter table empyyc modify lstname varchar(50);

--删除表
drop table;
```
##13、回收站
注意超级管理员没有回收站，只有普通用户才具有回收站。

```sql
--查看回收站
show recyclebin;

--查看回收站中的表
select * from "table";
--其中table为回收站中某个表的另外一个名字
SQL> drop table empyyc;

Table dropped.

show recyclebin;
ORIGINAL NAME	 RECYCLEBIN NAME		OBJECT TYPE  DROP TIME
---------------- ------------------------------ ------------ -------------------
EMPYYC		 BIN$lDaqFHTpNLPgU0NXH6xYgg==$0 TABLE	     2019-10-06:11:38:11

 select * from "BIN$lDaqFHTpNLPgU0NXH6xYgg==$0";

     EMPNO ENAME      JOB	       MGR  HIREDATE  SAL      COMM   DEPTNO
---------- ---------- --------- ---------- --------- ---------- ----------
7499 ALLEN      SALESMAN	      7698  20-FEB-81  1600        300  30
7521 WARD       SALESMAN	      7698  22-FEB-81  1250        500  30
7654 MARTIN     SALESMAN	      7698  28-SEP-81  1250       1400  30

purge recyclebin;
--清空回收站。
```
##14、练习强化
```sql
select  ename, hiredate  from emp  where deptno = (select deptno from emp where ename = 'SCOTT');
--注意字符串是单引号

select empno,ename , sal from emp where sal > (select avg(sal) from emp );


--查询各部门中平均工资比本部门平均薪资高的员工的员工号，姓名和工资(一般子查询)
select e.empno, e.ename, e.sal ,ee.avgsal from emp e, (select deptno, avg(sal) avgsal  from emp group by deptno) ee where  e.deptno = ee.deptno 
and sal > avgsal; 

   EMPNO ENAME	     SAL     AVGSAL
---------- ---------- ---------- ----------
      7499 ALLEN	    1600 1566.66667
      7566 JONES	    2975       2175
      7698 BLAKE	    2850 1566.66667
      7788 SCOTT	    3000       2175
      7839 KING 	    5000 2916.66667
      7902 FORD 	    3000       2175
  


select e.empno, e.ename, e.sal, (select avg(sal) from emp where deptno = 
e.deptno) avgsal from emp e where sal >((select avg(sal) from emp where deptno = e.deptno));
  EMPNO ENAME	     SAL     AVGSAL
---------- ---------- ---------- ----------
      7499 ALLEN	    1600 1566.66667
      7566 JONES	    2975       2175
      7698 BLAKE	    2850 1566.66667
      7788 SCOTT	    3000       2175
      7839 KING 	    5000 2916.66667
      7902 FORD 	    3000       2175

--查询姓名中含有u的员工所在相同部门的员工号和员工姓名
select b.empno, b.ename from emp b where deptno in(select deptno from emp a 
where a.ename like '%U%' );

--查询管理者是KING的员工和工资
select ename, sal from emp where mgr = (select empno from emp where ename = 'KING');
ENAME		  SAL
---------- ----------
JONES		 2975
BLAKE		 2850
CLARK		 2450
```








 






