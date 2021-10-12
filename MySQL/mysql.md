###  操作数据库

- 创建一个名称为mydb1的数据库

  ```mysql
  create database mybd1;
  ```

- 进入数据库

  ```mysql
  use mybd1;
  ```

- 查询当前数据库

  ```mysql
  select database();
  ```

- 查询数据库版本

  ```mysql
  select version();
  ```

- 查看和指定现有的数据库

  ```mysql
  show databases;
  ```

- 删除数据库

  ```MySQL
  drop database mydb1;
  ```

### 操作表

#### 数据库中的表

- 查看当前库中的表

  ```mysql
  show tables;
  ```

- 查看其他库中的表

  ```MySQL
  show tables from otherdatabase;
  ```

- 创建表

  ```mysql
  CREATE TABLE my_user(
  id INT(10),
  NAME VARCHAR(10),
  sex CHAR(2),
  enrollment_time DATE
  );
  ```

- 删除表

  ```mysql
  drop table my_user;
  ```

#### 表结构

- 查看表的结构

  ```
  desc my_user;
  ```

  

- 增加表结构

  ```mysql
  alter table my_user add birthday date;
  ```

- 修改表结构

  - 修改字段类型

  ```mysql
  alter table my_user modify id varchar(5);
  ```

  - 修改字段名称和字段类型

  ```mysql
  alter table my_user change birthday phone int(11);
  ```

- 删除表结构

  ```mysql
  alter table my_user drop birthday;
  ```

#### 表数据

- 插入数据

  ```mysql
  insert into my_user
  (id,name,sex,enrollment_time,phone)
  values(1,"yingzheng",'M','1999-1-1',1000),
  (4,"huamulan",'W',"1503-6-1",1003);
  ```

  **varchar,char,date类型的值需要加" "或' '**

  - 省略方式(不建议)

  ```MySQL
  insert into my_user
  values(1,"yingzheng",'M','1999-1-1',1000);
  ```

  - 插入日期

    str_to_date('1988-06-12','%Y-%m-%d')

  ```MySQL
  insert into my_user
  (id,name,sex,enrollment_time,phone)
  values(2,"mengtian",'M',str_to_date('1988-06-12','%Y-%m-%d'),1001);
  ```

  - now(系统日期)

  ```mysql
  insert into my_user
  (id,name,sex,enrollment_time,phone)
  values(3,"baiqi",'M',now(),1002);
  ```

- 表复制

  - 复制到新建表(有 as)

  ```mysql
  create table copy_user as select * from my_user
  ```

  - 将查询的数据直接放到已存在的表中(无 as)

  ```mysql
  insert into copy_user select * from my_user
  ```

- 更新数据

  ```mysql
  update my_user set name="yk" where id=3;
  ```

- 删除数据

  ```mysql
  delete from my_user where id=3	
  ```

#### 创建表加入约束

- 常见约束

  1. 非空约束，not null

     - 只有列级约束,没有表级约束
     - 不能为null

  2.  唯一约束，unique 

     - 有列级约束也有表级约束

     - 字段唯一,但可以多个字段为null

  3. 主键约束，primary key

     - 不能为null
     - 不能重复
     - 一张表只能有一个主键
     - 有列级约束也有表级约束

  4. 外键约束，foreign key

     - 无列级约束,有表级约束
     - 建立外键时所对应的父表的字段必须要创建唯一索引
     - 外键可以为null
       - 删除数据,先子后父
       - 添加数据,先父后子
       - 创建表,先父后子
       - 删除表,先子后父
     - 修改或删除父表数据子表数据会随之改变

```mysql
CREATE TABLE my_user(
id INT(10) not null,/*列级约束*/
NAME VARCHAR(10),
sex CHAR(2),
enrollment_time DATE
);
```

```mysql
CREATE TABLE my_user(
id INT(10) ,
NAME VARCHAR(10),
sex CHAR(2),
enrollment_time DATE,
unique(name,phone)/*表级约束*/
);
```

```mysql
CREATE TABLE my_user(
id INT(10) primary key auto_increment,/*列级约束 主键自增*/
NAME VARCHAR(10),
sex CHAR(2),
enrollment_time DATE
 /*PRIMARY key (id,name)*/
);
```

```mysql
/*父表*/
create table user(
idx int(10) ,
classroom varchar(10),
    primary key (idx)
);

/*子表*/
CREATE TABLE my_user(
id INT(10),
NAME VARCHAR(10),
sex CHAR(2),
enrollment_time DATE,
foreign key (id) references user(id)/*表级约束*/
);
```

### 查询数据

查询顺序

1. select 字段
2. from 表名
3. where …….(后面不能直接使用分组函数)
4. group by ……..
5. having …….(就是为了过滤分组后的数据而存在的—不可以单独的出现)
6. order by ……..



- 简单查询

  ```mysql
  select  id, name from my_user;
  ```

- 计算查询

  ```mysql
  select  id, name,phone+100 from my_user;
  ```

- 重名名

  ```mysql
  select  id as "编号", name as "名字",phone+100 as"电话号码" from my_user;
  ```

  **注意:字符串必须添加单引号 | 双引号**

- 条件查询

  |      运算符      |                             说明                             |
  | :--------------: | :----------------------------------------------------------: |
  |        =         |                             等于                             |
  |      <>或!=      |                            不等于                            |
  |        <         |                             小于                             |
  |        <=        |                           小于等于                           |
  |        >         |                             大于                             |
  |        >=        |                           大于等于                           |
  | between … and …. |               两个值之间,**等同于 >= and <=**                |
  |     is null      |                 为null（is not null 不为空）                 |
  |     **and**      |                             并且                             |
  |      **or**      |                             或者                             |
  |        in        |          包含，相当于多个or（not in不在这个范围中）          |
  |       not        |                not可以取非，主要用在is 或in中                |
  |       like       | like称为模糊查询，支持%或下划线匹配  %匹配任意个字符  下划线，一个下划线只匹配一个字符  %匹配任意个字符 |

  ```mysql
  select * from my_user where id<>2;
  ```

  ```mysql
  SELECT * FROM my_user WHERE enrollment_time BETWEEN "1980-1-1" AND "2000-1-1" 
  ```

  ```mysql
  SELECT * FROM my_user WHERE phone IN( 1002,1000 )
  ```

- 排序数据

  ```MySQL
  select * from emp order by sal asc;/*单字段排序*/
  ```

  ```mysql
  select * from emp order by job desc, sal desc;/*多字段排序*/
  ```

- 函数

  | count | 取得记录数 |
  | :---: | :--------: |
  |  sum  |    求和    |
  |  avg  |   取平均   |
  |  max  | 取最大的数 |
  |  min  | 取最小的数 |

  - Count(*)表示取得所有记录，为null的值也会取得

  ```mysql
  SELECT COUNT(*) FROM my_user;
  ```

  - 采用count(字段名称)，不会取得为null的记录

  ```mysql
  SELECT COUNT(phone) FROM my_user;
  ```

  - 采用count(distinct 字段名称)去除重复,不会取得为null的记录

  ```mysql
  select count(distinct phone) AS "有效电话数为" from my_user
  ```

  - sum(会忽略null)

  ```MySQL
  select sum(sal+IFNULL(comm, 0)) from emp;
  ```

- 分组查询

  - group by

  ```MySQL
  select job, sum(sal) from emp group by job;
  ```

  - having(分组数据再进行过滤)

  ```MySQL
  select job, avg(sal) from emp group by job having avg(sal) >2000;
  ```

  

### 连接查询

- 内连接( inner 可以省略)

  表1 inner join 表2 on关联条件

- 左外连接(outer 可以省略,且不建议加outer)

  表1 left outer join 表2 on 关联条件

- 右外连接(outer 可以省略,且不建议加outer)

  表1 right outer join 表2 on 关联条件

#### 子查询

子查询就是嵌套的select语句，可以理解为子查询是一张表

子查询可以跟在select ,where,from 后面

```mysql
select empno, ename, sal from emp where sal > (select avg(sal) from emp);
```

#### union

```MySQL
select * from emp where job='MANAGER'
union
select * from emp where job='SALESMAN'
```

合并结果集的时候，需要查询字段对应个数相同。

#### limit

```MySQL
select * from table limit m,n
/*其中m是指记录开始的index，从0开始，表示第一条记录
n是指从第m+1条开始，取n条。
select * from tablename limit 2,4
即取出第3条至第6条，4条记录*/
```

