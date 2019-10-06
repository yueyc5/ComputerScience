##1、 创建和管理表：表约束
约束是表一级的限制
如果存在依赖关系，约束可以防止错误删除数据，增加不符合规则的数据。   
约束的类型：

- 主键约束（Primay Key Coustraint） 唯一性，非空性   
- 唯一约束 （Unique Counstraint）唯一性，可以空，但只能有一个   
- 检查约束 （Check Counstraint）对该列数据的范围、格式的限制（如：年龄、性别等）   
- 默认约束 （Default Counstraint）该数据的默认值   
- 外键约束 （Foreign Key Counstraint）需要建立两表间的关系并引用主表的列  

```sql
not null, unique ,primary key,foreign key, check
```

```sql
create table teacher
(
 sid number constraint teacher_sid_pk primary key,
 --主键约束
 sname varchar2(20) constraint teacher_name_notnull not null,
 --非空约束
 email varchar2(20) constraint teacher_email_notnull not null
 						constraint teacher_email_unique unique,
 --唯一约束
 sex number constraint teacher_sex_min check(sex in(1,2)),
 --检查约束
 deptno number constraint teacher_deptno_pk references dept(deptno) on
 delete set null,
 --外键约束 +  外键的约束策略
 age number constraint teacher_age  check(age > 20)
 --检查约束
 );
 
 insert into teacher values(1, 'wxj', '110@qq.com', 1, 21)
 insert into teacher values(2, 'xwl', '150@qq.com', 2, 21)
```

外键约束策略。（但是一般不使用）

```sql
on delete cascade --当删除父表的时候，级联删除子表的记录。
--比如外键所在表的有一条记录删除，而这个主键在另外一个表中用到，那么相关联的表行也被删除。
on delete set null 将子表的相关依赖记录的外键置为null。
--比如外键所在的表的一条记录删除，而这个主键在另外一个表中用到，那么相关联的行的外键列会被设置为空。
```
##2、视图
-  视图就是表的逻辑抽象，本身没有数据，其数据还是来源于表。   
-  创建视图

```sql
create view  viewyyc as (select * from empyyc) [with check option][with read only];

crete view viewempas (select *from empyyc where deptno = 30) with check option；
--用户只能向视图中插入30号部门的数据，否则会出错。 
--注意的问题 分配权限:给scott用户开启权限
grant create view to scott;
```
-  视图的优点
  - 简化复杂查询、  
  我们在使用查询时，在很多时候我们要使用聚合函数，同时还要 显示其它字段的信息，可能还会需要关联到其它表，这时写的语句可能会很长，如果这个动作频繁发生的话，我们可以创建视图，这以后，我们只需要select * from view就可以啦，这样很方便。
  - 限制数据访问、  
    因为视图是虚拟的，物理上是不存在的，只是存储了数据的集合，我们可以将基表中重要的字段信息，可以不通过视图给用户，视图是动态的数据的集合，数据是随着基表的更新而更新。同时，用户对视图不可以随意的更改和删除，可以保证数据的安全性。
  - 逻辑上的独立性，屏蔽了真实表的结构带来的影响。  
    视图可以使应用程序和数据库表在一定程度上独立。如果没有视图，应用一定是建立在表上的。有了视图之后，程序可以建立在视图之上，从而程序与数据库表被视图分割开来。
  - 视图不能提高性能，只是一个快捷方式.
  
  
-  视图的缺点
  - 性能差：数据库必须把视图查询转化成对基本表的查询，如果这个视图是由一个复杂的多表查询所定义，那么，即使是视图的一个简单查询，数据库也要把它变成一个复杂的结合体，需要花费一定的时间。

  - 修改限制：当用户试图修改视图的某些信息时，数据库必须把它转化为对基本表的某些信息的修改，对于简单的视图来说，这是很方便的，但是，对于比较复杂的试图，可能是不可修改的。
 
- 视图的限制
  - 当视图定义中具有组函数、distinct、rownum的时候 不可以使用delete
  - 当视图定义中具有group by 、distinct、rownum、列的定义为表达式的时候，不可以使用update

##3、序列
- 序列的概念
  - 序列：一种可供多个用户用来产生唯一数值的数据库对象。
      - 自动提供唯一值
      - 共享对象
      - 主要用于提供主键
      - 将序列值放入内存可以提高访问效率
- 创建序列

```sql  
--创建序列
create sequence myseq increment by 10
start with 120
maxvalue 9999
nocycle --不循环
cache n --cache指定创建多少个值放到内存中
nocache; --没有chache
--第一次指向的是第一个元素的前面的元素
select myseq.nextval from dual;
--获取当前序列号
select myseq.currval from dual;
--删除当前序列
drop sequence myseq;
```
- 使用序列的注意问题
  - 多个表共用一个序列会导致序列不连续
  - 回滚也会造成序列不连续
  - 系统异常，内存序列丢失，也会造成序列不连续
 
##4、索引
 - 索引的基本知识
   - 索引和表示单独存放的、索引对于dba和开发人员是透明的，只需要创建索引。
 - 索引的基本原理

 - 索引的创建
 
 ```sql
 create index myindex on emp(deptno);
 create index myindex on emp(deptno, empno);
 ```
 - 索引的优缺点
 - 优点
 
  - 大大加快数据的检索速度;
  - 创建唯一性索引，保证数据库表中每一行数据的唯一性;
  - 加速表和表之间的连接;
  - 在使用分组和排序子句进行数据检索时，可以显著减少查询中分组和排序的时间。
- 缺点
 - 索引需要占物理空间。
 - 当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，降低了数据的维护速度。
##5、proc嵌入式编程

