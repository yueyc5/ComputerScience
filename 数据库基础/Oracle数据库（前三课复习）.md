
###1 查询员工姓名中含有下划线的员工 
```sql
SELECT *FROM emp	WHERE ename LIKE '%\_'ESCAPE '\';
```
###2 order by +多列 则排序结果由第一例决定 且依次受到之后列的影响。
--如：

```sql
SELECT *FROM emp ORDER BY deptno, empno;
```  
>先对emp按照deptno排序，再按照empno排序，由于一个部门有多个员工，因而排序结果是对相同部门的员工 按照员工号码再次进行排序。

###3 排序的时候 如何将空值排到最后
```sql
SELECT *FROM emp order by comm NULLS LAST;
```

###4  单行函数：接受参数返回一个结果 只对一行进行变换 每行返回一个结果 可以转换数据类型 可以嵌套 参数可以是一列或一个值
(1)字符函数
  
 ```sql 
 SELECT  INITCAP(ENAME) FROM emp;
 ```  
 >将所有的员工姓名由原来的任意状态改变成首字母大写的状态
 
 (2)日期处理函数 
  
  ```sql
  SELECT TO_CHAR(sysdate,'yyy-mm-dd hh12:mi:ss') FROM DATE;  
  ```    
  >打印本地时间  
  
   ```sql
   SELECT ename ,sysdate -hiredate from emp；  
   ```  
   >计算员工入职天数  

   ```sql
      SELECT MONTHS_BETWEEN(sysdate,hiredate)FROM EMP;
   ```  
   >精确计算员工入职月数  
   
   ```sql 
   SELECT ROUND(SYSDATE ,'MONTH') FROM dual; 
   ```
   >计算当前月份如：今天是2018-11-19 则会显示的是01-12月-18  
   
(3)数据类型转换  
隐式类型转换：可以把日期转换成数字进行比较如 
  
```sql   
SELECT '111'+222 FROM dual;
```   
按照加减乘除的逻辑进行运算
 ```sql   
 SELECT '111'||222 FROM dual;  
```  
按照字符串拼接的逻辑进行运算  

显示转换：字符串转数字 字符串转日期 日期转字符串  数字转字符串to_char函数对数字的转换
  
```sql
SELECT ename ,to_char(sal,'L9,999.99') FROM EMP;  
```  
```sql   
SELECT ename,to_number('￥199,322.33'，'L9,999.99') FROM emp;
```
(4) IF THEN – ELSE
 简单case语句  
 CASE  *** WHEN ***  THEN ****
 ELSE *** END   
    
 >eg :给总裁涨工资1000 给经理涨工资800 其他人 500.    
 
 ```sql   
 SELECT ename ,CASE JOB WHEN 'PRISIDENT' THEN SAL+1000 WHEN    
 'MANAGER' THEN SAL+800 ELSE SAL+500  END FROM emp;   
 ```
或者使用oracle语法 语法格式:   

```sql  
DECODE(COL|expression,search1,result1,..,..,default);     
SELECT ename,(decode(job,'prisident',sal+1000,'manager' ,sal +500, sal+200)) from emp;   
```   
case搜索语句   
eg: 如果员工的收入大于8000 则是高收入 5000 和8000之间 则是中等收入 否则是低收入.
   
```sql
SELECT ename，CASE  WHEN SAL>8000 THEN '高收入' WHEN SAL>=5000 AND SAL<=8000 THEN '中等收入' ELSE'低等收入'
 END '收入分级';   
```   
###5 多行函数又叫多行函数
分组函数作用于多行,对每组函数，只返回一个结果，group by用于分组  having 过滤分组结果集。  
注意 ：组函数具有自动滤空功能,做修正。所有的数据的检索，都必须在分组的结果之上 组函数除外  
eg: 求每个部门不同工种的平均工资
   
```sql  
SELECT deptno ,job Avg(sal) from emp  group by deptno,job;
```
>先按部门分组 再按照工种分组。

###6 条件过滤
WHERE子句和having子句的比较
先分组，再过滤
  
```sql
SELECT deptno ,avg(sal) from emp group by deptno having deptno =10;   
```   
先过滤，再分组 
  
```sql  
SELECT deptno,avg(sal) from emp where deptno =10 group by deptno;   
```   
###7 多表查询
查询员工的薪水级别

```sql   
SELECT e.ename,e.sal,s.* from  emp e,salgrade s where e.sal between s.losal and s.hisal;   
```
按部门统计员工人数：部门号 部门名称 各部门人数
###8 外连接技术
 右外连接 ：+写在等号的左边 那边少写哪边. 
 
 ```sql
    SELECT d.dname,d.deptno ,count(e.ename) from emp e,dept d where   
    e.deptno(+) = d.deptno group by d.deptno.d.dname;   
 ```
 ```sql
     SELECT d.dname.d.deptno,count(e.ename) from emp e right join dept d on where e.deptno  = d.deptno group    
     by d.deptno,d,dname;   
 ```
左外连接 ： + 写在等号的右边 那边少些哪边
###9  自连接技术	
如：查询员工的姓名和他老板的姓名(最大的boss 没有boss 也就是有个员工没有老板)
   
```sql
SELECT e.ename,s.ename from emp e ,emp s where e.mgr = s.empno(+);   
```  
###10 子查询
一般子查询
    相关子查询 将主查询的结果给子查询使用
    eg ：查询SALES部门的员工信息
(1)条件查询

```sql
    SELECT e.*  ,d.dname FROM emp  e,dept d where e.deptno =d.deptno and d.dname 
    ='sales';
```
(2)一般子查询 

```sql
SELECT e.* ,FROM emp e  WHERE e.deptno  =(SELECT deptno from dept where dname
 ='SALES');
```
###11 查询相同内容 子查询和 多表查询的比较 多表查询的效率比较高 以空间换时间
###12 子查询的位置放置
		单行子查询对应单行比较操作符 多行子查询对应多行比较操作符
		查询工种和id =7369 一样 且工资比7902 号员工号的员工信息
###13 in 指其中的一个值 any 是其中的一个 all 所有值

```sql
SELECT *FROM emp e,dept d WHERE e.deptno = d.deptno and sal>all(SELECT SAL FROM emp WHERE 
DEPTNO =30);
```
查询不是经理的员工信息 
  
```sql
SELECT * FROM emp WHERE empno NOT IN (SELECT mgr FROM emp WHERE MGR IS NOT NULL);   
```
###14 ORACLE 中特有语法
TopN问题 排序问题

```sql
	 SELECT *FROM (SELECT *FROM emp  ORDER BY SAL DESC) WHERE ROWNUM <=3;
```
 注：ROWNUM是ORACLE服务器返回的结果集的固有属性，结果集生成则其固定 对其进行的排序不会改变固有属性.因而 where子句中只能使用 <.<= 不能使用> 或者>=; 生成结果集是一行一行生成的，没有第一行，就没有第二行。只能这么用 
###15  分页问题
解决分页问题的思路 首先 应该对要取出的结果进行排序（如果需要的话），然后将其作为子表进行查询，将得到新的ROWNUB列，将该属性作为子表的新属性，这样，便可以使用该子表属性进行查询。

```sql
SELECT l.r,l.empno,l.ename from (SELECT ROWNUM ,e.* FROM (SELECT *FROM EMP ORDER BY sal DESC))
l where l.r >10  ; 
```
###16 找到本部门薪水大于本部门平均薪水的员工
(1)方法1 一般子查询
先计算每个部门的平均薪水，作为一个子表，原有员工表作为另外一个表。进行多表查询。根据员工号相同和 sal>avg(sal)进行表的连接

```sql
	SELECT *FROM emp e,(SELECT deptno.avg(sal) a FROM emp group by deptno )d 
	where e.deptno = d.deptno and e.sal >e.a;
```

 (2)方法2 相关子查询： 主查询 通过别名将主查询的值传递给子查询 从而限制每个子查询只返回一列 作为主查询的一列（主查询中的子句只能放置单行子查询否则 会出现一对多的矛盾）
  
  ```sql
  SELECT e.empno ,e.ename ,e.sal ,(SELECT avg(sal) avgsal from emp ee group by deptno where 
  e.empno  = ee.empno )sFROM emp WHERE e.sal>SELECT avg(sal) avgsal from emp ee 
  group by deptno where e.empno  = ee.empno;
  ```
###17 sql语句的分类
DML(data Manipulation Language) 数据库操作语言 增删改查  
DCL(data control language)数据库控制语言  提交 回滚 保存点（事务控制）  
 DDL(data Define Language) 数据库定义语言 创建表 所以 视图  同义词
###18 & 可以改变输入的变量值，进而进行批量操作
###19 创建和原有表相同的表头的空表
 
 ```sql
 CREATE table  emp_a as select *from emp WHERE 1 =0;
 ```
###20 将一个表数据批量插入另一个表

```sql
 		INSERT INTO emp_a SELECT *FROM emp ;
```
###21 更新数据
 		UPDATE emp set sal =10000 WHERE ENAME ='KING';
###22 删除数据
```sql
DELETE FROM EMP WHERE EMPNO =7369;
```
###23 TRUNCATE 与 DELETE 
 1 delete 逐条删除 truncate先摧毁再重建  
 2 delete是MDL语句 truncate是DDL语句   
 3 DML可以闪回 做错了 并且提交了 可以通过闪回 撤销操作 要开启闪回，必须开启行移动功能       
 4 delete不会释放空间 truncate会释放空间   
 5 delete 比oracle快
###24 Oracle中的事务
（1）显示提交 commit.  
（2）隐式提交 :  
&#160;&#160;&#160;&#160;&#160;&#160;&#160;1）执行DDL语句 如创建表之前有没有提交的update 或者insert语句.  
&#160;&#160;&#160;&#160;&#160;&#160;&#160;2）正常退出.  
（3）显示回滚 rollback.  
 &#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;隐式回滚 非法退出 掉电 宕机  系统出错
 
###26 Oracle 默认事务是打开的 但是不自动提交
	 Mysql 是自动提交的 默认不开启事务或者本身并不支持事务
###27 事务的概念
数据库事务(Database Transaction) ，是指作为单个逻辑工作单元执行的一系列操作，
要么完全地执行，要么完全地不执行。 事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源。通过将一组相关操作组合为一个要么全部成功要么全部失败的单元，可以简化错误恢复并使应用程序更加可靠。一个逻辑工作单元要成为事务，必须满足所谓的ACID（原子性、一致性、隔离性和持久性）属性。事务是数据库运行中的逻辑工作单位，由DBMS中的事务管理子系统负责事务的处理。
###28 sql99 支持的四种隔离级别
READ UNCOMMITED (读未提交) ：脏读，幻读  不可重复读  
READ COMMITED（读已提交）;幻读，不可重复读。（ORACLE默认的隔离级别）   
REPEATABLE READ（可重复读）幻读   
SERIALIZABLE（串行化） 限制并发，性能低下  
注：ORACLE 支持的2种事务隔离级别 READ COMMITED SERIALIZABLE 默认事务隔离级别为READ COMMITED 写的时候只允许一个写 读的时候多个读  
Mysql支持四种事务隔离级别 默认情况下是可重读读（REPEATABLE READ）。
###29 几个基本概念
脏读：读取了同一个数据库中被更新 但是还没有被提交的操作 若一旦这些操作被回滚，那么读取的内容就是临时和无效的。
不可重复读：对于同一个数据库中的更新前后，对原有字段的读取值发生了变化，这里主要是指对数据的更新
幻读：插入新的一行后，前后两次读取的行数不一致，会多出几行，这里主要是指对数据的插入
###30 事务的四个性质(ACID)
	（1）原子性(Atomicity)
			指的是事务包含的所有操作，要么全部成功，要么全部失败回滚。
	（2）一致性（Consistency）
			指的是必须使数据库从一个一致性状态变换到另一个一致性状态事务执行之前和执行之
			后都必须处于一致性状态。
	（3）隔离性
			指的是多用户并发访问数据库时，数据库为每一个用户开启事务，不能被其他事务的操
			作干扰，多个并发事务之间不能相互干扰。
	（4）持久性
			指的是对数据库的操作一旦提交，那么对数据集数据的改变是永久的。即使数据库系统
			遇到故障的情况下也不会丢失已经提交的事务的操作。
###31 ROWID指的是数据库的某一行的指针 指针相当于一个索引
```sql
		SELECT rowid ,t.* FROM T1 t; 
```
###32 修改表结构 属于DDL（数据库定义语言）
```sql
	ALTER TABLE T1 ADD tnamess varchar2(20);--增加列
	ALTER TABLE T1 MODIFY tname2 varchar2(40) --修改列的类型和大小
	ALTER TABLE T1 RENAME COMLUMN tnamess TO tnames --修改列的名字
	ALTER TABLE T1 DROP COLUMN tnames --
```
###33 表约束
```	sql	
		CREATE  TABLE TEACHER(tid number  constraint teacher_tid_pk primary key ,--主键约束
		tname  varcahr(20) constraint teacher_tname_notnull not null,--非空约束
		temail  varcahr(40) constraint teacher_temail_unique unique,--唯一约束
		tsex number constraint  teacher_tsex_check check(tsex in(1,2)),--只能为1或者2
		tage  number constraint teacher_tage_check check(tage >10)--只能大于10
		);
```
***主外键约束***
eg:给表增加外键约束

```sql
		alter table teacher  add dept .number  constraint student_fk references dept(deptno) 
		on delete set null;
```
###34 数据库对象

表: 基本的数据储存的集合由行和列组成  
视图: 从表中抽出的逻辑上相关的数据集合（表的逻辑抽象，视图本身没有数据，还是来源于表 视图相当于快捷方式 快速查看表.视图就是为了简化查询，限制数据访问、提供数据的相互独立性 同样的数据，可以有不同的显示方式. 
序列: 提供有规律的数值. 
索引:提高查询的效率. 
同义词: 给对象起别名
###35 视图创建
```sql
CREATE VIEW v_emp AS (SELECT *FROM emp WHERE deptno =10) WITH CHECK 
OPTION ;--限制只能插入10号部门的员工信息
```
视图中的DML语句 可以在简单视图中使用DML语句. 
当视图定义中包含了以下元素之一的时候 不能使用delete ：组函数、group by子句 distinct 
关键字 rownum伪列.  
当视图中定义包含了以下元素之一的时候，不能使用update:组函数，group by, distinct关键字，rownum伪列，列的定义为表达式.  
当视图中定义包含了以下元素之一的时候，不能使用insert：组函数，group by,distinct关键字，rownum伪列，烈的定义为表达式，表中非空的列在视图中的定义未包括.
###35 删除视图
```sql
		drop view v_emp;
```
###36 序列
```sql
CREATE Sequence dept_seq [INCREMENT BY N] [START WITH N][{MAXVALUE N | 
NOMAXVALUE}] [MINVALUE N |NOMINVALUE] [CYCLE |NOCYCLE] [CACHE N |NOCACHE]
```
可提供多个用户用来产生唯一数值的数据库对象,自动提供唯一的数值 共享对象 主要用于提供主键值 将序列值装入内存可以提高访问效率. 
注：多个表使用同一个序列回使序列不连续 回滚 系统异常也会使序列不连续。
###37 关于索引的小结
索引和表是单独存放的，索引对DBA和开发人员，使用起来是透明的的 只要创建索引。加快检索速度

```sql
	CREATE INDEX MYINDEX ON EMP(DEPTNO);
		CREATE INDEX MYINDEX ON EMP(DEPTNO,JOB);
```
索引 ：一种独立于表的模式对象，可以存在于表不同的磁盘中，或者表空间中，索引被删除或者损坏，不会对表产生影响，其影响的只是查询的速度。索引一旦建立，ORACLE管理系统会对其进行自动维护，而且Oracle管理系统决定何时会引用索引，用户不用再查询语句中指定使用哪一个索引 在删除一个表的时候,也会删除基于该表的索引 通过指针加速ORACLE服务器的查询速度 通过快速定位数据的方法，减少磁盘I/O