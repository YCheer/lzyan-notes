## 1. sql、DB、DBMS分别是什么，他们之间的关系是什么

- **DB：**

  database（数据库，数据库实际上就是在硬盘上以文件的形式存在）

- **DBMS：**

  Database Management System（数据库管理系统，常见的有：MySQL Oracle DB2 Sybase SqlServer..)

- **SQL：**

  结构化查询语言，是一门标准通用的语言。标准的sql适合于所有的数据库产品。

  SQL属于高级语言，只要能看懂英语单词就可以写出sql语句

  SQL语句在执行的时候，实际上内部也会进行编译，然后再执行sql。（sql语句的编译由DBMS完成）


`DBMS` 负责执行 `sql` 语句，通过执行 `sql` 语句来操作 `DB` 当中的数据

```
DBMS-（执行）->SQL-（操作)->DB
```

## 2. 什么是表

**表（table）是数据库的基本组成单位，所有的数据都以表格的形式组织，目的是使可读性强。**

一个表包括行和列：

- 行：被成为数据/记录（data）
- 列：被称为字段（column）

每一个字段应该包括哪些属性？字段名、数据类型、相关的约束。

## 3. 学习MySQL主要还是学习通用的SQL语句，那么SQL语句包括增删改查，SQL语句怎么分类

- **DQL（数据查询语言）：** 查询语句，凡是 select 语句都是 DQL
- **DML（数据操作语言）：** insert delete update，对表当中的数据进行增删改
- **DDL（数据定义语言）：** create drop alter，对表结构的增删改  
- **TCL（事务控制语言）：** commit 提交事务，rollback 回滚事务
- **DCL（数据控制语言）：** grant 授权，revoke 撤销权限等

## 4. MySQL的常用命令

- show databases;（查看有哪些数据库）
- create database user;（创建名user的数据库）
- use user;（使用user这个数据库）
- select database();（查看当前使用的数据库)
- show tables;（查看当前数据库有什么表）
- source sql的文件路径 （导入sql文件初始化数据）
- drop database user;（删除名为user的数据库） 
- drop table user;（删除名为user的表） 
- desc 表名;（查看表的结构）
- \c 结束一条命令
- exit\quit；退出mysql
- show create table user; （查看创建user这个表的sql语句）

## 5. 对SQL脚本的理解

当一个文件的扩展名是 `.sql`，并且该文件中编写了大量的 `sql` 语句，我们称这样的文件为 `sql` 脚本。

直接使用 `source` 命令可以直接执行 `sql` 脚本。

## 6. SQL语句的应用

注意：

1. 任何一条 `sql` 语句都以 `;` 结尾。
2. `sql` 语句不区分大小写。
3. 标准 `sql` 语句中要求字符串使用单引号括起来，虽然 `mysql` 支持双引号但是尽量不用，因为不通用。
4. 在数据库中 `NULL` 不是一个值，代表什么也没有，为空，空不是一个值不能用等号衡量，必须使用 `is null` 或者 `is not null`
5. 在数据库中只要有数学表达式出现 `null`，最后的结果都是 `null`
6. `mysql` 与 `oracle` 数据库相比，`oracle` 数据库的语法会相对严谨一点，比如查询一个数据时表中的数据是大写的 `varchar` 类型的此时你的 `SQL` 语句中的查询条件就必须要是大写的否则查询不出来，而在 `msyql` 中无论小写大写都可以查询出来

### DQL

执行 `bjpowernode.sql` 脚本导入数据
  ```sql
DROP TABLE IF EXISTS EMP;
DROP TABLE IF EXISTS DEPT;
DROP TABLE IF EXISTS SALGRADE;

CREATE TABLE DEPT
       (DEPTNO int(2) not null ,
	DNAME VARCHAR(14) ,
	LOC VARCHAR(13),
	primary key (DEPTNO)
	);
CREATE TABLE EMP
       (EMPNO int(4)  not null ,
	ENAME VARCHAR(10),
	JOB VARCHAR(9),
	MGR INT(4),
	HIREDATE DATE  DEFAULT NULL,
	SAL DOUBLE(7,2),
	COMM DOUBLE(7,2),
	primary key (EMPNO),
	DEPTNO INT(2) 
	)
	;

CREATE TABLE SALGRADE
      ( GRADE INT,
	LOSAL INT,
	HISAL INT );

INSERT INTO DEPT ( DEPTNO, DNAME, LOC ) VALUES ( 10, 'ACCOUNTING', 'NEW YORK'); 
INSERT INTO DEPT ( DEPTNO, DNAME, LOC ) VALUES ( 20, 'RESEARCH', 'DALLAS'); 
INSERT INTO DEPT ( DEPTNO, DNAME, LOC ) VALUES ( 30, 'SALES', 'CHICAGO'); 
INSERT INTO DEPT ( DEPTNO, DNAME, LOC ) VALUES ( 40, 'OPERATIONS', 'BOSTON'); 
commit;
 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7369, 'SMITH', 'CLERK', 7902,  '1980-12-17', 800, NULL, 20); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7499, 'ALLEN', 'SALESMAN', 7698,  '1981-02-20', 1600, 300, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7521, 'WARD', 'SALESMAN', 7698,  '1981-02-22', 1250, 500, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7566, 'JONES', 'MANAGER', 7839,  '1981-04-02', 2975, NULL, 20); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7654, 'MARTIN', 'SALESMAN', 7698,  '1981-09-28', 1250, 1400, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7698, 'BLAKE', 'MANAGER', 7839,  '1981-05-01', 2850, NULL, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7782, 'CLARK', 'MANAGER', 7839,  '1981-06-09', 2450, NULL, 10); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7788, 'SCOTT', 'ANALYST', 7566,  '1987-04-19', 3000, NULL, 20); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7839, 'KING', 'PRESIDENT', NULL,  '1981-11-17', 5000, NULL, 10); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7844, 'TURNER', 'SALESMAN', 7698,  '1981-09-08', 1500, 0, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7876, 'ADAMS', 'CLERK', 7788,  '1987-05-23', 1100, NULL, 20); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7900, 'JAMES', 'CLERK', 7698,  '1981-12-03', 950, NULL, 30); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7902, 'FORD', 'ANALYST', 7566,  '1981-12-03', 3000, NULL, 20); 
INSERT INTO EMP ( EMPNO, ENAME, JOB, MGR, HIREDATE, SAL, COMM, DEPTNO ) VALUES ( 7934, 'MILLER', 'CLERK', 7782,  '1982-01-23', 1300, NULL, 10); 
commit;
 
INSERT INTO SALGRADE ( GRADE, LOSAL, HISAL ) VALUES ( 1, 700, 1200); 
INSERT INTO SALGRADE ( GRADE, LOSAL, HISAL ) VALUES ( 2, 1201, 1400); 
INSERT INTO SALGRADE ( GRADE, LOSAL, HISAL ) VALUES ( 3, 1401, 2000); 
INSERT INTO SALGRADE ( GRADE, LOSAL, HISAL ) VALUES ( 4, 2001, 3000); 
INSERT INTO SALGRADE ( GRADE, LOSAL, HISAL ) VALUES ( 5, 3001, 9999); 
commit;
```

- #### 简单查询：
```sql
select 字段名1,字段名2,字段名3,...from 表名;（查询全部字段换成*，实际开发中不建议使用，效率很低）
```

- #### 数学运算：
```sql
select 字段名*12 from 表名;
```

- #### 查询结果的列重命名：
```sql
select 字段名 as 重命名 from 表名; （带中文的加''，as关键字可以省略)
```

- #### 条件查询：
```sql
  #执行顺序：先from，然后where，最后select
  1.单条件：select 字段 from 表名 where 条件;
  2.多条件：select 字段 from 表名 where 条件 and 条件;
  3.范围条件：select 字段 from 表名 where 字段名 between 1000 and 3000;
  （取得是1000-3000之间的闭区间，between and在使用的时候必须是左边是小的，右边是大的）
  （betwwen and也可以用来操作字符串，使用在字符串的时候是左闭右开的）
  4.空\不为空:select 字段 from 表明 where 字段名 is null\is not null;
  5.and和or联合使用：and的优先级会比or高，所以在当运算符的优先级不确定的时候加小括号。
  6.in相当于or：
  	(1)select 字段 from 表名 where 字段='lzyan' or 字段='lzy';
      (2)select 字段 from 表名 where 字段 in('lzyan','lzy');
  7.模糊查询like：%代表任意多个字符，_代表任意一个字符。注意：查询_和%这种关键字符的时候需要加转义符"\"
```

- #### 排序查询：
```sql
select 字段 from 表名 order by 字段;（默认为升序，asc表示升序，desc表示降序）
select 字段 from 表名 order by 字段 asc;
select 字段 from 表名 order by 字段 desc;
select 字段 from 表名 order by 字段 desc, 字段 asc;（多字段排序，越靠前的字段起的主导作用越大,只有当前面的字段相等的时候，后面的字段才会使用。或者说，只有当前面的字段无法完成排序的时候，才会启用后面的字段）
select 字段 from 表名 order by 1;（以数字的形式，1表示按第一列的升序排序，这是一种不健壮的方式，该写死）
select * from 表名 where 条件 order by ...（这条语句的执行顺序：from->where->select->order by）
语句执行排序的验证方式：
select 字段名 as 别名 from 表名 order by 别名;（可以看出，是按照别名这一列来排序的，先从表中查到这个字段然后再来起别名，最后再按别名来排序）
例子：select ename,sal as salary from emp order by salary;
```

- #### 分组函数：

  1. count 计数 2.sum 求和 3.avg 平均值 4.max 最大值 5.min 最小值
  2. 分组函数都是对”某一组“数据进行操作的
  3. 分组函数的另外一个名字：**多行处理函数**
  4. 多行处理函数的特点：输入多行，最终输出的结果是一行
  5. 分组函数自动忽略null
  6. SQL语句中有一个语法规则，分组函数不可直接使用在where子句当中
  7. 分组函数也能组合起来使用


  ```sql
  select count(字段) from 表名; （计算所填字段的记录总数，输出的是一行，自动忽略null）
  select count(*) from 表名; （计算总记录条数，输出的是一行，和某个字段无关）
  select sum(字段) from 表名; 
  select avg(字段) from 表名;
  select max(字段) from 表名;
  select min(字段) from 表名;
  select sum(字段) from 表名 where 字段 is not null;（这是错误的，不需要额外添加这个过滤条件，sum函数自动忽略null） 
  select count(*),min(字段),sum(字段),avg(字段),max(字段) from 表名;
  ```

- #### 单行处理函数：

    1. 输入一行，输出一行，进去多少行出来多少行
    2. 所有数据库都是这样规定，只要有null参与运算的结果一定是null
  3. ifnull() 空处理函数（可能为null的数据，被当做什么处理），属于单行处理函数

```sql
  select ifnull(字段,0) from 表名;
```

- #### group by 和 having：

    1. group by：按照某个字段或者某些字段进行分组
    2. having：对分组之后的数据进行再次过滤
    3. 分组函数一般会和group by联合使用，这也是为什么它叫分组函数的原因，并且任何一个分组函数都是在group by语句执行结束之后才会执行，当一条sql语句没有group by的话，整张表的数据会自成一组
    4. sql语句当中有一个语法规则，分组函数不可以直接使用where子句中，是因为group by这个语句是在where执行之后才会执行的
    5. 有分组函数的sql语句后面有个缺省的group by
    6. 当一条语句中，有group by的话，select后面只能跟分组函数和参与分组的字段（例：select sum(count),name from user group by name;)
    7. 多个字段可联合起来一块分组
    8. having只是在对分完组后的数据进行过滤，但能够使用where过滤的尽量使用where过滤，使用having效率比较低
    9. having是group by的搭档，只有使用了group by才能使用having ，即having就是为了过滤分组后的数据而存在的，不可以单独出现

关于查询结果的去重：

 distinct只能出现在所有字段的最前面，表示的是后面的字段联合起来去重

```sql
select distinct 字段 from 表名
例子：select distinct deptno,job from emp;后面的字段联合起来去重，不是仅仅去deptno这个字段
与分组函数联用
select count(distinct 字段) from 表名
统计岗位的数量：select count(distinct job) from emp;
```

- #### 总结一个完整的DQL语句怎么写：

```sql
  select .. from .. where .. group by .. having .. order by ..
  顺序:5       1       2         3           4           6
  1.选择哪一张表
  2.先执行where语句过滤原始数据
  3.执行group by进行分组
  4.执行having对分组数据进行操作
  5.执行select选出数据
  6.执行order by排序
  原则：能在where中过滤的数据，尽量在where中过滤，效率较高。having的过滤就是专门对分组之后的数据及进行过滤的
```

- #### 连接查询：

  1. 在实际开发中，大部分得情况下都不是从单表中查询数据，一般都是多张表联合查询取出最终的结果

  2. 在实际开发中，一般一个业务都会对应多张表，比如：学生和班级，起码两张表

  3. 连接查询的分类：

     根据语法出现的年代来划分的话，包括： SQL92（一些比较老的DBA可能还在使用这种语法，DBA:DataBase Administrator，数据库管理员）、SQL99（比较新的语法）

     根据表的连接方式来划分，包括：

     内连接：等值连接、非等值连接、自连接

     外连接：左外连接、右外连接

     全连接（这个很少用）

在表的连接查询方面有一种现象被称为：笛卡尔现象（笛卡尔乘积现象），笛卡尔现象：当两张表进行连接查询的时候，没有任何条件进行限制，最终的查询结果条数是两张表记录数的乘积

关于表的别名：

```sql
select e.ename,d.dname from emp e,dept d;
```

表的别名有什么好处：

​	第一：执行效率高

​	第二：可读性好

怎么避免笛卡尔现象？加条件进行过滤

思考：避免了笛卡尔现象，会减少记录的匹配次数吗？

​			不会，底层照样还是这么多次，只不过显示的是有效记录

案例：找出每一个员工的部门名称，要求显示员工名和部门名

```sql
select e.ename,d.dname from emp e,dept d where e.deptno = d.deptno;//SQL92
```

 1. 内连接之等值连接：最大特点是-条件是等量关系
   案例：查询每个员工的部门名称，要求显示员工名和部门名
   ```sql
   sql92：
   select e.ename,d.dname from emp e dept d where e.deptno = d.deptno;
   sql99（常用）:
   select e.ename,d.dname from emp e join dept d on e.deptno = d.deptno;
   sql99语法结构更清晰一些：表的连接条件和后来的where条件分离了
   ```
 2. 内连接之非等值连接：最大特点是-连接条件中的关系是非等量关系
   案例：找出每个员工的工资等级，要求显示员工名、工资、工资等级
   ```sql
   select e.ename,e.sal,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
   ```
 3. 内连接之自连接：最大的特点是-一张表看作两张表，自己连接自己
   案例：找出每个员工的上级领导，要求显示员工名和对应的领导名
   ```sql
   员工的领导编号=领导的员工编号
   select a.ename as '员工名',b.ename as '领导名' from emp a inner join emp b on a.mgr = b.empno;
   ```
 4. 外连接：
   什么是外连接，和内连接有什么区别？
   内连接：假设A和B表进行连接，使用内连接的话，凡是A表和B表能够匹配上的记录查询出来，这就是内连接。
   外连接：假设A和B表进行连接，使用外连接的话，AB两张表中有一张表是主表，一张表是副表，主要查询主表中的数据，捎带着查询副表，当副表中的数据没有和主表中的数据匹配上，副表自动模拟出NULL与之匹配
   外连接的分类：
   ​	左外连接（左连接）：表示左边的这张表是主表
   ​	右外连接（右连接）：表示右边的这张表是主表
   ​	左连接有右连接的写法，右连接也会有对应的左连接的写法
   案例：找出每个员工的上级领导（所有员工必须查询出来）
   ```sql
   内连接：
   inner可以省略
   select a.ename '员工名',b.ename '领导名' from emp a inner join emp b on a.mgr = b.empno;
   外连接:
   左连接
   select a.ename '员工名',b.ename '领导名' from emp a left join emp b on a.mgr = b.empno;
   outer可以省略
   select a.ename '员工名',b.ename '领导名' from emp a left outer join emp b on a.mgr = b.empno;
   右连接
   select a.ename '员工名',b.ename '领导名' from emp b right join emp a on a.mgr = b.empno;
   
   为什么inner、outer可以省略？
   因为区分内连接和外连接不是依靠这两个单词
   ```
   外连接最重要的特点是：主表的数据无条件的全部查询出来
   案例：找出哪个部门没有员工
   ```sql
   select d.* from emp e right join dept d on e.deptno = d.deptno where e.empno is null;
   ```
 5. 三张表怎么连接查询
   案例：找出每个员工的部门名称以及工资等级
   ```sql
   A join B join C on 
   表示：A表和B表进行表连接，连接之后A表继续和C表进行连接
   select e.ename,d.dname,s.grade from emp e join dept d on e.deptno = d.deptno join salgrade s on e.sal between s.losal and s.hisal;
   ```
   案例：找出每一个员工的部门名称、工资等级、以及上级领导
   ```sql
   select e.ename,d.dname,s.grade,e1.ename from emp e join dept d on e.deptno = d.deptno join salgrade s on e.sal between s.losal and s.hisal left join emp e1 on e.mgr = e1.empno;
   ```
- #### 子查询：
   什么是子查询？子查询都可以出现在哪里
   select语句当中嵌套select语句，被嵌套的select语句是子查询
   select
   ​	...(select)
   from
   ​	...(select)
   where 
   ​	...(select)
   where子句中使用子查询
   案例：找出高于平均薪资的员工信息
   ```sql
   select * from emp where sal> avg(sal);//错误写法，where后面不能直接使用分组函数
   第一步：找出平均薪资
   select avg(sal) from emp;
   第二步：where过滤
   select * from emp where sal >2073.214286
   第一步和第二部合并：
   select * from emp where sal> (select avg(sal) from emp);
   ```
   from后面嵌套子查询
   案例：找出每个部门平均薪水的薪资等级
   ```sql
   第一步：找出每个部门平均薪水（按照部门编号分组，求sal的平均值）
   select deptno,avg(sal) as avgsal from emp group by deptno;
   第二步：将以上的查询结果当做临时表t，让t表和salgrade s表连接，条件是：t.avgsal between s.losal and s.hisal 
   select t.*,s.grade from (select deptno,avg(sal) as avgsal from emp group by deptno) t 
   join salgrade s on t.avgsal between s.losal and s.hisal;
   ```
   案例：找出每个部门平均的薪水等级
   ```sql
   第一步：找出每个员工的薪水等级
   select e.ename,e.sal,e.deptno,s.grade from emp e join salgrade s on e.sal between s.losal and s.hisal;
   第二步：基于以上结果，继续按照deptno分组，求grade平均值
   select e.deptno,avg(s.grade) from emp e join salgrade s on e.sal between s.losal and s.hisal group by e.deptno;
   ```
   select 后面嵌套子查询
   案例：找出每个员工所在的部门名称，要求显示员工名和部门名
   ```sql
   select e.ename,d.dname from emp e join dept d on e.deptno = d.deptno;
   select e.ename,(select d.dname from dept d where e.deptno = d.deptno) as dname from emp e;
   ```
- #### union(可以将查询结果集相加)
   案例：找出工作岗位是SALESMAN和MANAGER的员工
   ```sql
   第一种：
   select ename,job from emp where job = 'MANAGER' or job = 'SALSMAN'；
   第二种：
   select ename,job from emp where job in('MANAGER','SALESMAN');
   第三种
   select ename,job from emp where job = 'MANAGER' union select ename,job from emp where job = 'SALESMAN';
   ```
   两张不相干的表中的数据拼接在一起显示
   ```sql
   select ename from emp union select dname from dept;
   ```
- #### limit（重点中的重点，分页查询）:
   1. limit是mysql特有的，其他数据库没有，不通用。（Oracle中有一个相同的机制，叫做rownum）
   2. limit取结果集中的部分数据，这是它的作用
   3. 语法机制：startIndex，length
      ​				  startIndex表示起始位置
      ​				  length表示取几个
      案例：取出工资前5名的员工（思路：降序取前5个）
      ```sql
      select ename,sal from emp order by sal desc;
      取前5个：
      select ename,sal from emp order by sal desc limit 0,5;
      select ename,sal from emp order by sal desc limit 5;
      ```
   4. limit是sql语句最后执行的一个环节
      select 		5
      ​	...
      from		1
      ​	...
      where		2
      ​	...
      group by	3
      ​	...
      having		4
      ​	...
      order by	6
      ​	...
      limit			7
      ​	...
      案例：找出工资排名在第4到第9名的员工
      ```sql
      select ename,sal from emp order by sal desc limit 3,6;
      ```
   5. 通用的标准分页sql
      每页显示3条记录：
      第1页：0，3
      第2页：3，3
      第3页：6，3
      第4页：9，3
      第5页：12，3
      每页显示pageSize条记录：
      第pageNo页：(pageNo-1) * pageSize,pageSize
      pageSize是什么？是每页显示多少条几率
      pageNo是什么？显示第几页
      Java代码（
      ​	int pageNo = 2; //页码是2
      ​	int pageSize = 10; // 每页显示10条
      ）

### DDL和DML

  - #### 建表语句的语法格式：

  ```sql
  create table 表名(
  	字段名1 数据类型,
      字段名2 数据类型,
      字段名3 数据类型,
      ...
  );
  ```

  关于MySQL当中字段的数据类型，常见的

  ```mysql
  int 整数型（Java中的int）
  bigint 长整型（java中的long)
  float 浮点型（Java中的 float double）
  char 定长字符串（String）
  varchar 可变长字符串（StringBuffer、StringBUilder）
  date 日期类型（对应Java中的java.sql.Date类型）
  BLOB 二进制大对象（存储图片、视频等流媒体信息，对应Java中的object）Binary Large Object
  CLOB 字符大对象（存储较大文本，比如可以存储4G的字符串）Character Large Object
  
  char和varchar怎么选择？
  在实际的开发中，当某个字段中的数据长度不发生改变的时候，是定长的，例如：性别、生日等都采用char
  当一个字段的数据长度不确定，例如：简介、姓名等都是采用varchar
  BLOB和CLOB类型的使用？
  电影表：t_movie
  id(int) name(varchar) playtime(date/char) haibao(BLOB) history(CLOB)
  表名在数据库当中一般建议以：t_或者tbl_开始
  创建学生表：
  	学生信息包括
  		学生、姓名、性别、班级编号、生日
  		学号：bigint
  		姓名：varchar
  		性别：char
  		班级编号：varchar
  		生日：char
  	create table t_student(
      	no bigint,
          name varchar(255),
          sex char(1),
          classno varchar(255),
          birth char(10)
      );
  ```

  - #### insert语句插入数据：
  ```sql
  语法格式：
  
  ​	insert into 表名(字段名1，字段名2，字段名3，...) valuse(值1，值2，值3,...)

  ​	要求：字段的数量和值的数量相同，并且数据类型要对应相同 

  ​	insert into 表名 valuse(...)

  ​	要求：跟每个字段相对应不能乱，有几个写几个，列的数量和值的数量相匹配

  ​	insert into 表名 (字段...) values(...),(...),(...),(...)

  ​	一次插入多行数据

  需要注意的地方：

  ​	当一条insert语句执行成功后，表格当中必然会多一行数据

  ​	即使多的这一行记录当中某些字段是NULL，后期也没有办法在执行

  ​	insert语句插入数据了，只能使用update进行更新

  表的复制：

  ​	语法：create table 表明 as select语句;

  ​	将查询结果当作表创建出来

  将查询结果插入到一张表中

  insert into dept1 select * from dept

  ```

- #### 修改数据：update

```sql
语法格式：

​update 表名 set 字段名1=值1，字段名2=值2...where 条件：

​注意：没有条件整张表数据全部更新

​案例：将部门10的LOC修改为SHANGHAI，将部门名称修改为RENSHIBU

update dept1 set loc = 'SHANGHAI',dname='RENSHIBU' where deptno =10;
```

- #### 删除数据:

```sql
语法格式：	

delete from 表名 where 条件：

注意：没有条件全部删除

怎么删除大表（重点）

truncate table 表名 //表被截断，不可回滚,永久丢失
  
对于表结构的修改，大多使用工具完成，因为实际开发中一旦设计好之后，对表结构的修改是很少的，修改表结构就是对之前的设计进行了否定，即使需要修改表结构，也可以直接使用工具操作。

修改表结构的语句不会出现在Java代码中。

出现在Java代码中的sql包括：insert delete update select（这些都是表中的数据操作）

CRUD-->C：create、R：retrieve、U：update、D：delete
```

- #### 约束（Constraint）：

  ​	什么是约束？常见的约束有哪些？

  ​	在创建表的时候，可以给表的字段添加相应的约束，添加约束的目的是为了保证表中数据的合法性、有效性、完整性。

  ​	常见的约束有哪些？

  ​		1. 非空约束（not null）：约束的字段不能为null

  ​		2. 唯一约束（unique）：约束的字段不能重复

  ​		3. 主键约束（primary key）：约束的字段既不能为null，也不能重复

  ​		4. 外键约束（foreign key）

  ​		5. 检查约束（check）：主义Oracle数据库有check约束，但是mysql没有，目前mysql不支持该约束		

- #### 唯一约束：
  
  ​	唯一约束修饰的字段具有唯一性，不能重复。但是可以为null
  
  ​	案例： 给某一列添加unique
  
  ```sql
  drop table if exists t_user;
  create table t_user (
  	id int,
  	username varchar(255) unique //列级约束
  );
  insert into t_user values(1,'zhangshan');
  insert into t_user values(2,'zhangshan');
  ERROR 1062 (23000): Duplicate entry 'zhangshan' for key 'username'
  ```
  
  ​	案例：给两个列或者多个列添加unique
  
  ```sql
  drop table if exists t_user;
  create table t_user (
  	id int,
  	usercode varchar(255),
  	username varchar(255),
  	unique(usercode,username)//多个字段联合添加一个约束unique 表级约束
  );
  insert into t_user values(1,'111','zs');
  insert into t_user values(2,'111','ls');
  insert into t_user values(3,'222','zs');
  select * from t_user;
  insert into t_user values(3,'111','zs');
  ERROR 1062 (23000): Duplicate entry '111-zs' for key 'usercode'
  
  drop table if exists t_user;
  create table t_user (
  	id int,
  	usercode varchar(255) unique,
  	username varchar(255) unique //列级约束
  );
  insert into t_user values(1,'111','zs');
  insert into t_user values(2,'111','ls');
  ERROR 1062 (23000): Duplicate entry '111' for key 'usercode'
  
  ```
  
  注意：not null约束只有列级约束。没有表级约束
  
- #### 主键约束：
  
  **怎么给一张表添加主键约束**
  
  ```sql
  drop table if exists t_user;
  create table t_user(
  	id int primary key,//列级约束
  	username varchar(255),
  	email varchar(255)
  );
  insert into t_user (id,username,email) values(1,'zs','zs@123.com');
  insert into t_user (id,username,email) values(2,'ls','ls@123.com');
  insert into t_user (id,username,email) values(3,'ww','ww@123.com');
  select * from t_user;
  insert into t_user (id,username,email) values(1,'jack','jack@123.com');
  ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
  insert into t_user (username,email) values('jack','jack@123.com');
  ERROR 1364 (HY000): Field 'id' doesn't have a default value
  ```
  
  根据以上的测试得出：id是主键，因为添加主键约束，主键字段中的数据不能为null，也不能重复，主键的特点是——不能为null，也不能重复
  
  **主键相关的术语** 
  
  主键约束、主键字段、主键值
  
  **主键有什么作用**
  
  ​表的设计三范式有要求，第一范式久要求任何一张表都应该有主键。
  
  ​	主键的作用：主键值是这样记录在这张表当中的唯一标识。（就像一个人的身份证号码一样）
  
  ​	一张表的主键约束只能有一个
  
  **主键的分类**
  
  ​根据主键字段的字段数量来划分：
  
  ​		单一主键（推荐的，常用的）
  
  ​		复合主键（多个字段联合起来添加一个主键约束，不建议使用，因为复合主键违背三范式）
  
  ​	根据主键性质来划分：
  
  ​		自然主键（推荐的，常用的，主键值最好就是一个和业务没有任何关系的自然数）
  
  ​		业务主键（主键值和系统的业务挂钩，例如：拿着银行卡的卡号做主键，拿着身份证号码作为主键）
  
  ​		最好不要拿着和业务挂钩的字段作为主键，因为以后的业务一旦发生改变的时候，主键值可能也需要随着		发生变化，但是有得时候没有办法发生变化，因为变化可能导致主键值重复
  
  使用表级方式定义主键：
  
  ```sql
  drop table if exists t_user;
  create table t_user(
  	id int,
  	username varchar(255),
  	primary key(id)
  );
  insert into t_user(id,username) values(1,'zs');
  insert into t_user(id,username) values(2,'ls');
  insert into t_user(id,username) values(3,'ws');
  insert into t_user(id,username) values(4,'cs');
  select * from t_user;
  insert into t_user(id,username) values(4,'cx');
  ERROR 1062 (23000): Duplicate entry '4' for key 'PRIMARY'
  ```
  
  mysql提供了主键值自增：
  
  ```sql
  drop table if exists t_user;
  create table t_user(
  	id int primary key auto_increment,
  	username varchar(255)
  );
  insert into t_user(username) values('a');
  insert into t_user(username) values('b');
  insert into t_user(username) values('c');
  insert into t_user(username) values('d');
  insert into t_user(username) values('e');
  insert into t_user(username) values('f');
  select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | a        |
  |  2 | b        |
  |  3 | a        |
  |  4 | b        |
  |  5 | c        |
  |  6 | d        |
  |  7 | e        |
  |  8 | f        |
  +----+----------+
  ```
  
  Oracle当中也提供了一个自增机制，叫做序列（sequence）对象
  
- #### 外键约束：
  
  关于外键约束的相关术语：
  
  ​	1. 外键约束： foreign key
  
  ​	2. 外键字段：添加有外键约束的字段
  
  ​	3. 外键值：外键字段中的每一个值
  
  业务背景：
  
  ​	请设计数据库表，用来维护学生和班级的信息
  
  ```
  	第一种方案：一张表存储所有数据
  
  ​	no            		  name            	  classno              		classname
  
  -------------------------------------------------------------------------------------
  
  ​	1                     zs1                    101                       高三一班
  
  ​	2                     zs2                    101                       高三一班
  
  ​	3                     zs3                    101                       高三一班
  
  ​	4                     zs4                    102                       高三二班
  
  ​	5                     zs5                    102                       高三二班
  缺点：冗余，不推荐
  ```
  
  ```
  	第二种方案：两张表（班级表和学生表）
  
  ​	t_class 班级表
  
  ​	 cno (pk)            		 cna
  
  ---------------------------------------------------------------------------------
  
  ​	101 						高三一班
  
  ​	102 						高三2班
  
  ​	t_student学生表
  
  ​	sno(pk)							sname			classno(该字段添加外键约束fk)
  
  ----------------------------------------------------------------------------------------------
  
  ​	1								zs1				101
  
  ​	2								zs2				101
  
  ​	3								zs3				102
  
  ​	4								zs4				102
  
  ​	5								zs5				102
  ```
  
  将以上表的建表语句写出来：
  
  ​t_student中的classno字段引用t_class表中的cno字段，此时t_student表叫做子表，t_class表叫做父表
  
  顺序要求:
  
  ​	删除数据的时候，先删除子表，再删除父表
  
  ​	添加数据的时候，先添加父表，再添加子表
  
  ​	创建表的时候，先创建父表，再创建子表
  
  ​	删除表的时候，先删除子表，再删除父表
  
  ```sql
  drop table if exists t_student;
  drop table if exists t_class;
  
  create table t_class(
  	cno int,
  	cname varchar(255),
  	primary key(cno)
  );
  create table t_student(
  	sno int,
  	sname varchar(255),
  	classno int,
  	primary key(sno),
  	foreign key(classno) references t_class(cno)
  );
  
  insert into t_class values(101,'xxxxxxx');
  insert into t_class values(102,'ssssssss');
  
  insert into t_student values(1,'zs1',101);
  insert into t_student values(2,'zs2',101);
  insert into t_student values(3,'zs3',102);
  select * from t_class;
  select * from t_student;
  
  insert into t_student values(4,'lisi',103);
  ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`test`.`t_student`, CONSTRAINT `t_student_ibfk_1` FOREIGN KEY (`classno`) REFERENCES `t_class` (`cno`))
  外键值可以为null
  ```
  
  **外键字段引用其他表的某个字段的时候，被引用的字段必须是主键吗？**
  
  注意：被引用的字段不一定是主键，但至少具有unique约束

## 7. 存储引擎

  完整的建表语句

  ```sql
  CREATE TABLE ’t_x'(
  	'id' int(11) DEFAULT NULL
  ) ENGINE=innoDB DEFAULT CHARSET=utf8;
  注意：在mysql中，凡是标识符是可以使用飘号括起来的，最好别用，不通用
  建表的时候可以指定存储引擎，也可以指定字符集
  mysql默认使用的存储引擎是InnoDB方式
  默采用的字符集是UTF8
  ```

### 什么是存储引擎

  存储引擎这个名字只有在mysql中存在（Oracle中有对应的机制，但是不叫存储引擎。Oracle中没有特殊的名字，就是“表的存储方式”）

  mysql支持很多存储引擎，每一个存储引擎都对应了一种不同的存储方式

  每一个存储引擎都有自己的优缺点，需要在合适的实际选择合适的存储引擎

### 查看当前mysql支持的存储引擎

  show engines \G

  数据库5.7.21版本的存储引擎有

  ```sql
  *************************** 1. row ***************************
        Engine: InnoDB
       Support: DEFAULT
       Comment: Supports transactions, row-level locking, and foreign keys
  Transactions: YES
            XA: YES
    Savepoints: YES
  *************************** 2. row ***************************
        Engine: MRG_MYISAM
       Support: YES
       Comment: Collection of identical MyISAM tables
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 3. row ***************************
        Engine: MEMORY
       Support: YES
       Comment: Hash based, stored in memory, useful for temporary tables
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 4. row ***************************
        Engine: BLACKHOLE
       Support: YES
       Comment: /dev/null storage engine (anything you write to it disappears)
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 5. row ***************************
        Engine: MyISAM
       Support: YES
       Comment: MyISAM storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 6. row ***************************
        Engine: CSV
       Support: YES
       Comment: CSV storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 7. row ***************************
        Engine: ARCHIVE
       Support: YES
       Comment: Archive storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 8. row ***************************
        Engine: PERFORMANCE_SCHEMA
       Support: YES
       Comment: Performance Schema
  Transactions: NO
            XA: NO
    Savepoints: NO
  *************************** 9. row ***************************
        Engine: FEDERATED
       Support: NO
       Comment: Federated MySQL storage engine
  Transactions: NULL
            XA: NULL
    Savepoints: NULL
  ```

### 常见的存储引擎

  ```sql
        Engine: MyISAM
       Support: YES
       Comment: MyISAM storage engine
  Transactions: NO
            XA: NO
    Savepoints: NO
    MyISAM这种存储引擎不支持事务
    MyISAM采用三个文件组织一张表：
    	xxx.frm（存储格式的文件）
    	xxx.MYD（存储表中数据的文件）
    	xxx.MYI（存储表中索引的文件）
    优点：可以被压缩，节省存储空间。并且可以转换为只读表，提高检索效率
    缺点：不支持事务
    ------------------------------------------------------------------
        Engine: InnoDB
       Support: DEFAULT
       Comment: Supports transactions, row-level locking, and foreign keys
  Transactions: YES
            XA: YES
    Savepoints: YES
    优点：支持事务、行级锁，外键等。这种存储引擎数据的安全得到保障。
    表的结构存储在xxx.frm文件中
    数据存储在tablespace这样的表空间中（逻辑概念），无法被压缩，无法转换成只读
    这种InnoDB存储引擎在MySQL数据库崩溃之后提供自动恢复机制
    InnoDB支持级联删除和级联更新
    ---------------------------------------------------------------------
        Engine: MEMORY
       Support: YES
       Comment: Hash based, stored in memory, useful for temporary tables
  Transactions: NO
            XA: NO
    Savepoints: NO
    缺点：不支持事务，数据容易丢失。因为所有数据和索引都存储在内存当中
    优点：查询速度最快
    以前叫HEPA引擎
  ```

## 8. 事务

### 什么是事务

一个事务是一个完整的业务逻辑单元，不可再分。

比如：银行账户转账，从A账户向B账户转账。需要执行两条update语句。

```sql
update t_act set balance = balance - 10000 where actno = 'act-001';
update t_act set balance = balance + 10000 where actno = 'act-002';
```
以上两条DML语句必须同时成功，或者同时失败，不允许出现一条成功，一条失败。

要想保证以上的两条DML语句同时成功或者同时失败，那么久需要使用数据库的“事务机制“

### 和事务相关的语句

  只有DML语句（insert delete update)

  因为它们这三个语句都是和数据库当中得“数据”相关的，事务的存在是为了保证数据的完整性，安全性

  假设所有的业务都能使用1条DML语句搞定，还需要事务机制吗？不需要

  但实际情况不是这样，通常一个事（事务【业务】）需要多条DML语句共同联合完成

### 事务的原理

  假设一个事儿，需要先执行一条insert，再执行一条update，最后执行一条delete，这个事儿才算完成

  那么他的执行过程是：

  ​	开启事务机制（开始）

  ​	执行insert语句-->insert......（这个执行成功之后，把这个执行记录到数据库的操作历史当中，并不会向文件中保存一条数据，不会真正的修改硬盘上的数据）

  ​	执行update语句-->update......（这个执行也是记录一下历史操作，不会真正的修改硬盘上的数据）

  ​	执行delete语句-->delete......（这个执行也是记录一下历史操作【记录到缓存】，不会真正修改硬盘上的数据）

  ​	提交事务或者回滚事务（结束）

  提交事务的时候同时修改硬盘上的数据和删除历史操作记录，回滚事务就是直接删除操作历史记录

### 事务的特性

  事务包括四大特性：ACID

  A:原子性：事务是最小的工作单元，不可再分

  C:一致性：事务必须保证多条DML语句同时成功或者同时失败

  I:隔离性：事务A于事务B之间具有隔离

  D:持久性：持久性说的是最终数据必须持久化到硬盘文件中，事务才算成功的结束

### 关于事务之间的隔离性

事务的隔离性存在隔离级别，理论上隔离级别包括4个：

- 第一级别：读未提交（read uncommitted）

  对方事务还没提交，我们当前事务可以读取到对方未提交的数据

  读未提交存在脏读（Dirty Read）现象：表示读到了脏的数据

- 第二级别：读已提交（read committed）

  ​	对方事务提交之后的数据我方可以读取到

  ​	这种隔离级别解决了：脏读现象

  ​	读已提交存在的问题是：不可重复读（在当前事务还未结束的时候，不能读到相同的数据）

- 第三级别：可重复读（repeatable read）

  ​	这种隔离级别解决了：不可重复读的问题

  ​	这种隔离级别存在的问题是：读取到的数据是幻象

- 第四级别：序列化读/串行化读

  ​解决了所有问题

  ​但效率低，需要事务排队

  Oracle数据库默认的隔离级别是：读已提交

  mysql数据库默认的隔离级别是：可重复读
  
### 演示事务
  
  mysql事务默认情况下是自动提交的
  
  （什么是自动提交？只要执行任意一条DML语句则提交一次）怎么关闭自动提交？
  
  start transaction;
  
  演示：
  
  ```sql
  drop table if exists t_user;
  create table t_user(
  	id int primary key auto_increment,
  	username varchar(255)
  );
  mysql> insert into t_user(username) values('zs');
  Query OK, 1 row affected (0.01 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  +----+----------+
  1 row in set (0.00 sec)
  
  mysql> rollback;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  +----+----------+
  1 row in set (0.00 sec)
  
  使用start transaction;关闭自动提交机制
  mysql> start transaction;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> insert into t_user(username) values('lisi');
  Query OK, 1 row affected (0.01 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  |  2 | lisi     |
  +----+----------+
  2 rows in set (0.00 sec)
  
  mysql> insert into t_user(username) values('wangwu');
  Query OK, 1 row affected (0.00 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  |  2 | lisi     |
  |  3 | wangwu   |
  +----+----------+
  3 rows in set (0.00 sec)
  
  mysql> rollback;
  Query OK, 0 rows affected (0.01 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  +----+----------+
  1 row in set (0.00 sec)
  
  commit提交
  mysql> start transaction;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> insert into t_user(username) values('wangwu');
  Query OK, 1 row affected (0.01 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  |  4 | wangwu   |
  +----+----------+
  2 rows in set (0.00 sec)
  
  mysql> commit;
  Query OK, 0 rows affected (0.00 sec)
  
  mysql> select * from t_user;
  +----+----------+
  | id | username |
  +----+----------+
  |  1 | zs       |
  |  4 | wangwu   |
  +----+----------+
  2 rows in set (0.00 sec)
  
  
  savepoint name
  rollback name
  回滚到某个点
  ```

## 9. 索引
### 什么是索引、有什么用

索引相当于一本书的目录，通过目录可以快速的找到对应的资源

在数据库方面，查询一张表的时候有两种检索方式：

- 第一种方式：全表扫描

- 第二种方式：根据索引检索（效率很高）

### 索引为什么可以提高检索的效率

其最根本的远离是缩小了扫描的范围

索引虽然可以提高检索效率，但是不能随意的添加索引，因为索引也是数据库当中的对象，也需要数据库不断的维护。是有维护成本的。

比如，表中的数据经常被修改这样久不适合添加索引，因为数据一旦修改，索引需要重新排序，进行维护

添加索引是给某一个字段，或者说某些字段添加索引
```sql
select ename,sal from emp where ename = 'smith';
```
当ename字段上没有添加索引的时候，以上sql语句会进行全表扫描，扫描ename字段中所有的值

当ename字段上添加索引的时候，以上sql语句会根据索引扫描，快速定位

### 什么时候考虑给字段添加索引（满足什么条件）

- 数据量庞大（根据客户的需求，根据线上的环境）
- 该字段很少的DML操作（因为字段进行修改操作，索引也需要进行维护）
- 该字段经常出现在where子句中（经常根据哪个字段查询）

注意：主键和具有unique约束的字段自动会添加索引
所以，根据主键查询效率较高，尽量根据主键检索

### 怎么创建、删除、查看索引
- 创建索引对象：
  ```sql
  create index 索引名称 on 表名(字段名);
  ```
- 删除索引对象:

  ```sql
  drop index 索引名称 on 表名;
  ```
- 查看索引

  ```sql
  show index from emp;
  ```

### 查看sql语句的执行计划：
```sql
explain select ename,sal from emp where sal= 5000;
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | emp   | ALL  | NULL          | NULL | NULL    | NULL |   14 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
```
从type列中可看到值为ALL，所以这是全表检索

### 给薪资sal字段添加索引

```sql
create index emp_sal_index on emp(sal);
```

查询执行计划

```sql
explain select ename,sal from emp where sal= 5000;
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys | key           | key_len | ref   | rows | Extra       |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
|  1 | SIMPLE      | emp   | ref  | emp_sal_index | emp_sal_index | 9       | const |    1 | Using where |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
```
可发现类型是ref，并且rows为1只扫描了一行

### 索引的实现原理简单描述
索引底层采用的数据结构是：B+Tree

通过B+Tree缩小扫描范围，底层索引进行了排序，分区，索引会携带数据在表中的“物理地址”，最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中的数据，效率是最高的
```sql
select ename from emp where ename = 'SMITH';
通过索引转换为:
select ename from emp where 物理地址 = 0x3;
```

### 索引的分类
- 单一索引：给单个字段添加索引
- 复合索引：给多个字段联合起来添加1个索引
- 主键索引：主键上会自动添加索引
- 唯一索引：有unique约束的字段上会自动添加索引
- ···

### 索引什么时候失效
```sql
select ename from emp where ename like '%A%';
```
模糊查询的时候，第一个通配符使用的是%，这个时候索引是失效的

## 10. 视图

### 什么是视图
站在不同的角度去看待数据（同一张表的数据，通过不同的角度去看待）
- 视图是一种根据查询（也就是SELECT表达式）定义的数据库对象，用于获取想要看到和使用的局部数据。 
- 视图有时也被成为“虚拟表”。 
- 视图可以被用来从常规表（称为“基表”）或其他视图中查询数据。
- 相对于从基表中直接获取数据，视图有以下好处：
  1. 访问数据变得简单 
  2. 可被用来对不同用户显示不同的表的内容

### 怎么创建、删除、修改视图
```sql
create view myview as select empno,ename from emp;
drop view myview;
alter view myview as slect empno from emp;
```
注意：只有DQL语句才能以视图对象的方式创建出来

对视图进行增删改查，会影响到原表数据（通过视图影响原表数据的，不是直接操作的原表）

### 视图的作用
视图可以隐藏表的实现细节。保密级别比较高的系统，数据库只对外提供相关的视图，程序员只对视图对象进行CRUD


## 11. DBA命令
### 新建用户
```sql
create user username identified by 'password'
说明:username——你将创建的用户名, password——该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登陆服务器.
例如：
create user p361 identified by '123';
可以登录但是只可以看见一个库  information_schema
```
### 授权
  命令详解
  ```sql
  grant all privileges on dbname.tbname to 'username'@'login ip' identified by 'password' with grant option;
    1.dbname=*表示所有数据库
    2.tbname=*表示所有表
    3.login ip=%表示任何ip
    4.password为空，表示不需要密码即可登录
    5.with grant option;  表示该用户还可以授权给其他用户
  ```
- 细粒度授权

首先以root用户进入mysql，然后键入命令：
```sql
grant select,insert,update,delete on *.* to p361 @localhost Identified by "123"; 
```
如果希望该用户能够在任何机器上登陆mysql，则将localhost改为 "%" 。

- 粗粒度授权


测试用户一般使用该命令授权:
```sql
GRANT ALL PRIVILEGES ON *.* TO 'p361'@'%' Identified by "123"; 
```
注意:用以上命令授权的用户不能给其它用户授权,如果想让该用户可以授权,用以下命令:
```sql
GRANT ALL PRIVILEGES ON *.* TO 'p361'@'%' Identified by "123"  WITH GRANT OPTION;
```
其中privileges包括:
```sql
1. alter：修改数据库的表
2. create：创建新的数据库或表
3. delete：删除表数据
4. drop：删除数据库/表
5. index：创建/删除索引
6. insert：添加表数据
7. select：查询表数据
8. update：更新表数据
9. all：允许任何操作
10. usage：只允许登录
```
### 回收权限
  ```sql
  revoke privileges on dbname[.tbname] from username;
  revoke all privileges on *.* from p361;
  use mysql
  select * from user
  进入 mysql库中
  修改密码;
  update user set password =  password('qwe') where user = 'p646';
  刷新权限;
  flush privileges
  ```


### 导出
导出整个数据库
```dos
  mysqldump bjpowernode>D:\bjpowernode.sql -uroot -pxxx
```
导出指定库下的指定表
```dos
mysqldump bjpowernode emp> D:\ bjpowernode.sql -uroot 
```
### 导入
```sql
create database bjpowernode;
use bjpowernode;
source D:\bjpowernode.sql 
```

## 12. 数据库设计的三范式

### 什么是设计范式
设计表的依据，按照这个三范式设计的表不会出现数据冗余
### 三范式有哪些
- 第一范式：

任何一张表都应该有主键，并且每一个字段原子性不可再分

- 第二范式：

建立在第一范式基础之上，所有非主键字段完全依赖主键，不能产生部分依赖

在典型的**多对多**关系设计中，一般会采用：三张表，关系表两个外键

- 第三范式：

建立在第二范式之上，所有非主键字段直接依赖主键，不能产生传递依赖

**一对多**关系中，采用：两张表，多的表加外键

在实际的开发中：以满足客户的需求为主，有的时候会拿数据的冗余换执行速度

### 一对一关系怎么设计
一对一设计有两种方案
- 主键共享
  ```sql
  t_user_login 用户登录表
  id(pk)    uername   password
  --------------------------------
    1         ls        123
    2         zz        234
  t_user_detail 用户详情信息表
  id(pk+fk)  realname  tel ····
  --------------------------------
    1         李四      1234
    2         杂志      1321
  ```

- 外键唯一
  ```sql
  t_user_login 用户登录表
  id(pk)    uername   password
  ------------------------------
    1         ls        123
    2         zz        234

  t_user_detail 用户详情信息表
  id(pk)    realname    tel    userid(fk+unique)····
  ---------------------------------------
    1         李四      1234      2
    2         杂志      1321      1
  ```

## 13. 参考

[老杜带你学习 Mysql](https://www.bilibili.com/video/BV1fx411X7BD?spm_id_from=333.999.0.0)