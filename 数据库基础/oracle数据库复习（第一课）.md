#oracle复习第一课
##1、查看系统参数
```sql
set linesize 300
select * from v$nls_parameters;
```
| PARAMETER|VALUE    |
---|:--:|---:
NLS_LANGUAGE			|				 AMERICAN
NLS_TERRITORY			|				 AMERICA
NLS_CURRENCY			|				 $
NLS_ISO_CURRENCY		|				 AMERICA
NLS_NUMERIC_CHARACTERS|				 .,
NLS_CALENDAR				|			 GREGORIAN
NLS_DATE_FORMAT 			|			 DD-MON-RR
NLS_DATE_LANGUAGE		|			 AMERICAN
NLS_CHARACTERSET			|			 AL32UTF8
NLS_SORT					|		 BINARY
NLS_TIME_FORMAT 			|			 HH.MI.SSXFF AM
NLS_TIMESTAMP_FORMAT	|					 DD-MON-RR HH.MI.SSXFF AM|
NLS_TIME_TZ_FORMAT		|				 HH.MI.SSXFF AM TZR
NLS_TIMESTAMP_TZ_FORMAT |					 DD-MON-RR HH.MI.SSXFF AM TZR|
NLS_DUAL_CURRENCY	|					 $
NLS_NCHAR_CHARACTERSET|						 AL16UTF16
NLS_COMP					|		 BINARY
NLS_LENGTH_SEMANTICS	|					 BYTE
NLS_NCHAR_CONV_EXCP		|				 FALSE

##2、显示系统时间（注意：oracle中日期格式敏感，日期类型可以和字符串类型相互比较）
```sql
select *from emp where hiredate <’01-oct-99’;
```
| EMPNO | ENAME | JOB | MGR | HIREDATE | SAL | COMM| DEPTNO|
---|:--:|---:|:--:|---:|:--:|---:|:--:|---:
7369 |SMITH  |CLERK|7902 |	17-DEC-80| 800 | |20|
7499 |	ALLEN |SALESMAN|	7698 |20-FEB-81|1600 |300|30|
7521 |WARD|SALESMAN	| 7698| 22-FEB-81|1250|50|30|
7566|	 JONES|MANAGER|7839| 02-APR-81|  2975| |20|
7654 |	MARTIN |SALESMAN	|7698 |28-SEP-81|1250 |1400|30|
7698 |BLAKE |	 MANAGER |	  7839|	 01-MAY-81|2850 ||   30|
7782|	 CLARK   | MANAGER	| 7839 |09-JUN-81| 2450 || 10|
7788 |SCOTT  |ANALYST	| 7566| 19-APR-87|3000 ||   20
7839 |KING   |PRESIDENT |	 |17-NOV-81|5000 ||10|
7844| TURNER |SALESMAN	 |  7698 |08-SEP-81|1500 | 0|30
7876 |ADAMS| CLERK	 |  7788| 23-MAY-87|1100||   20|
7900 |JAMES  | CLERK	|  7698 | 03-DEC-81|950 | |  30|
7902| FORD| ANALYST	| 7566| 03-DEC-81|3000||  20|
7934 |MILLER | CLERK	 | 7782 | 23-JAN-82|   1300 || 10|

##3、有关where的条件过滤(between 和and
```sql
select *from emp where sal between 1000 and 2000;
```
| EMPNO | ENAME | JOB | MGR | HIREDATE | SAL | COMM| DEPTNO|
---|:--:|---:|:--:|---:|:--:|---:|:--:|---: 
 7499   | ALLEN  |      SALESMAN  |	      7698   | 20-FEB-81 |  1600     |     300|	   30
 7521| WARD     |    SALESMAN  |	     7698 |22-FEB-81| 1250    |      500| 30
  7654  |MARTIN   |   SALESMAN	 |      7698  |28-SEP-81 |1250    |    1400 |	   30 |
  7844 | TURNER   |   SALESMAN	   |    7698  |08-SEP-81 |  1500 	   |    0	 |     30
 7876  |ADAMS    |   CLERK	     |  7788  |23-MAY-87 |1100 || 20 |
 7934  |MILLER  |    CLERK	    |   7782 |23-JAN-82 |1300 | |10 |
 
 ```sql
 select *from emp where sal >=1000  and sal <=2000;  
 ```
    注：oracle检索表的时候是按照从小到大的一行行匹配，因此如果改变成为2000-1000，那么条件永远不成立。

##4、有关in的使用

```sql
--查找在10号部门或者在20部门或者不在任何部门的员工。
Select *from emp where deptno  in (10,20,null);

--查找不在10号部门并且不在20部门并且不在任何部门的员工。
Select *from emp where deptno not in (10,20,null);
``` 

    结论:not in遇上空值 : 含有空值,则 where 条件表达式永远不成立.
##5、like模糊查询
     %代表0个或者多个任意字符  
     _代表一个任意字符   
     \代表转义字符
     6、order by 排序

##6、order by
```sql
--对员工信息按照部门从小到大进行排序（升序），如果部门变好相同则按照工资从大到小排序（降序）
select * from emp order by deptno asc, sal desc;
```
    ————默认情况下，order by 是升序的，并且 asc（desc）只负责最近的一列。

##7、关于null值的处理
   
   ——null与任何值运算都为空  
   —-null和空值和0不一样  
   —-where 子句中 not in（10，20，null）永远不成立。  
   —-将为空的排列列放置到最后面。

```sql
Select * from emp order by comm  desc nulls last;
Select * from emp order by comm  asc nulls last;
```
##8、单行函数和多行函数

   单行函数：对单一行进行处理，返回一个结果。  
   多行函数：对多行进行处理，返回一个结果。  
   
```sql
--字符串处理函数
 select lower(‘helloWOrld') from dual;
---日期处理函数
 select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual;

--按指定格式显示薪水（将数字转换成字符串）
--L 本地化、 ,千分符、.99 小数位数。
select ename, to_char(sal, 'L9,999.999') from emp;
--将数字转化为字符串
select to_number('$1,600.00', 'L9,999.99')  tostring from dual;
```
##9、条件表达式

```sql
---给员工涨工资：总裁涨800，经理涨500，其他人涨300
select ename, job,  sal ,(CASE job WHEN 'PRESIDENT' THEN sal+1000 WHEN 
'MANAGER' THEN sal+800 ELSE sal+500 END)
dd from emp;
 select ename, job,  sal ,(decode(job, 'PRESIDENT', sal+1000, 'MANAGER', sal+800,sal+500)) 
 dd from emp;
```

##10、group by 分组

```sql
 --求各个部门的平均工资 
Select deptno, avg(sal) from emp group by deptno;
--所有的检索结果，必须建立在分组的基础上。(分组函数除外)
--求不同部门的job相同的员工的平均工资
Select deptno, job , avg(sal) from emp group by deptno,job; 
```
##11、分组过滤
```sql
--求部门平均工资大于2000的部门。
Select deptno,  avg(sal) a from emp group by deptno having avg(sal) > 2000;

—-where条件过滤和having的比较
--最大区别在于where子句中不能有分组函数。
```
##12、多表连接
```sql
--按部门统计员工人数：部门号，部门名称，各部门人数
--各部门人数是在员工表中，部门名称在部门表中

Select d.deptno, d.dname , count(*) from dept d, emp e where d.deptno = 
e.deptno group by d.deptno, d.dname;

--我们发现缺少40号部门。 我们希望显示40号部门，这里就是外链接技术
Select d.deptno, d.dname , count(*) from dept d, emp e where d.deptno =
e.deptno(+) group by d.deptno, d.dname;
---(左外链接： 左边有 右边没有)

Select d.deptno, d.dname , count(*) from dept d left join  emp e  on d.deptno 
= e.deptno group by d.deptno, d.dname;
```

##13、重要查询回顾
```sql
--选择公司中有奖金的员工姓名，工资和奖金级别
select ename, sal , case when comm < 100 then 1 when comm >=100 and comm < 300
 then 2 else 3 end from emp where comm >=0;  
--选择姓名中有字母a和e的员工姓名
Select  ename from emp where ename like  '%a%e%' or ename like '%e%a%';
---查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
 Select empno , ename, sal, sal*1.20 "new salary" from emp;
---注意当字符串中有空格的时候需要使用双引号。

--查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内

Select e.mgr , d.ename, min(e.sal) from emp e , emp d where e.mgr = d.empno 
 and  e.mgr is not null  group by e.mgr , d.ename having min(e.sal) > 1000;
 
 --查询所有部门的名字，location_id，员工数量和工资平均值
 Select d.dname , d.deptno, count(*), avg(sal) from dept  d, emp e where  d.deptno = e.deptno group by d.deptno, d.dname;

DNAME		   DEPTNO   COUNT(*)   AVG(SAL)
-------------- ---------- ---------- ----------
ACCOUNTING	       10	   3 			2916.66667
RESEARCH	       20	   5	   		2175
SALES		       30	   6 			1566.66667


 Select d.dname , d.deptno, count(sal), avg(sal) from dept  d, emp e where  
 d.deptno = e.deptno(+) group by d.deptno, d.dname;

DNAME		   DEPTNO   COUNT(SAL)   AVG(SAL)
-------------- ---------- ---------- ----------
ACCOUNTING	       10	    3  2916.66667
OPERATIONS	       40	    0 
RESEARCH	       20	    5	   2175
SALES		       30	    6    1566.66667







