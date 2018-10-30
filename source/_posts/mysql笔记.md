---
title: mysql笔记
date: 2018-10-16 17:43:17
tags: [doc]
---

### 第一章 初涉Mysql
#### 一、 启动关闭 MySQL
mac && windows
```
mysql.server start
mysql.server stop

net start mysql
net stop mysql
```

| 参数                  | 描述        |
|:--------------------|:----------|
| -D, --database=name | 打开指定数据库   |
| --delimiter=name    | 指定分隔符     |
| －h, --host=name     | 服务器名称     |
| －p, --password=name | 密码        |
| －P, --port=#        | 端口号       |
| －-prompt=name       | 设置提示符     |
| －u, --user=name     | 用户名       |
| －V, --version=name  | 输出版本信息并退出 |

mysql 默认端口 3306  
默认超级管理员 root

#### 二、 MySQL 提示符
1. 进入MySQL环境前， --prompt 跟随参数
2. 进入MySQL环境后， 输入prompt 跟随参数

| 参数 | 描述     |
|:---|:-------|
| \D | 完整的日期  |
| \d | 当前的数据库 |
| \h | 数据库名称  |
| \u | 当前用户   |

#### 三、 退出 MySQL
```
exit;
quit;
\q;
```

#### 四、 MySQL语句的规范
1. 关键字与函数名称全部大写
2. 数据库名称、表名称、字段名称全部小写
3. SQL语句必须以分号结尾

#### 五、 常用命令
1. 显示当前服务器版本 `SELECT VERSION();`
2. 显示当前日期时间 `SELECT NOW();`
3. 显示当前用户 `SELECT USER();`
4. 创建数据库 `CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name;`  
    花括号为必选的，中间竖线代表从中选择，中括号可有可无
5. 查看数据库 `SHOW {DATABASES | SCHEMAS};`
6. 查看警告信息 `SHOW WARNINGS;`
7. 修改数据库 `ALTER {DATABASE | SCHEME} [db_name];`
8. 删除数据库 `DROP {DATABASE | SCHEMA} [IF EXISTS] db_name;`
9. 删除数据表 `DROP TABLE [IF EXISTS] table_name;`


----------


### 第二章 数据类型与操作数据表
#### 一. MySQL数据类型之整型
数据存储选用合适的数据类型，而不是最大的数据类型，节省空间

#### 二、数据表操作（插入记录与查找记录）
##### 1) 创建数据表
```
CREATE TABLE [IN NOT EXISTS] table_name(
    column_name data_type [NOT NULL] [UNIQUE KEY] [PRIMARY KEY],
);

1. NOT NULL 非空约束
2. unique key 唯一约束
2. default 默认约束
3. primary key 主键约束
    * 每张数据表当中只能存在一个主键
    * 主键保证记录的唯一性
    * 主键自动为 NOT NULL

mysql> create table tbl(
    -> id smallint auto_increment primary key,
    -> username varchar(30) not null unique key,
    -> age tinyint unsigned,
    -> salary float(8, 2) unsigned
    -> );
```

##### 2) 查看数据表结构 `SHOW COLUMNS FROM table_name`
```
mysql> show columns from tbl;
+----------+---------------------+------+-----+---------+----------------+
| Field    | Type                | Null | Key | Default | Extra          |
+----------+---------------------+------+-----+---------+----------------+
| id       | smallint(6)         | NO   | PRI | NULL    | auto_increment |
| username | varchar(30)         | NO   | UNI | NULL    |                |
| age      | tinyint(3) unsigned | YES  |     | NULL    |                |
| salary   | float(8,2) unsigned | YES  |     | NULL    |                |
+----------+---------------------+------+-----+---------+----------------+
```
##### 3) 插入记录 `INSERT [INTO] table_name [column_name, ...] VALUES(val, ...);`
`insert tbl (username, age, salary) values('gena', 25, 19);`

##### 4) 记录查找 `SELECT expr,... from table_name`
```
mysql> select * from tbl;
+----+----------+------+--------+
| id | username | age  | salary |
+----+----------+------+--------+
|  1 | tom      |   23 |  17.00 |
|  2 | john     |   24 |  18.00 |
|  3 | gena     |   25 |  19.00 |
+----+----------+------+--------+
3 rows in set (0.00 sec)
```

##### 5) 显示当前所使用的数据库 `SELECT DATABASE();`

##### 6) 查看数据表 `SHOW TABLES [from db_name]`
    1. 查看当前数据库的数据表 `show tables` 
    2. 查看其它数据库的数据表 `show tables from db_name`

----------


### 第三章 约束以及修改数据表
![内容总结](./../images/2018-10-24-mysql/第三章.jpg)
#### 一. 约束
1. 约束保证数据的完整性和一致性
2. 约束分为表级约束和列级约束
    * 列级约束：约束只针对一个字段
    * 表级约束：约束针对两个及以上的字段
3. 约束类型包括：
    * NOT NULL 非空约束
    * PRIMARY KEY 主键约束
    * UNIQUE KEY 唯一约束
    * DEFAULT 默认约束
    * FOREIGN KEY 外键约束（保持数据一致性，完整性。实现一对一或一对多关系） 


#### 二、 MySQL外间约束的要求解析
1. 父表和子表必须使用相同的存储引擎，而且禁止使用临时表。  
    编辑数据表的默认存储引擎 MySQL配置文件中修改 `default-storage-engine=INNODB`
2. 数据表的存储引擎只能为 InnoDB
3. 外键列和参照列必须具有相似的数据类型。其中数字的长度或是否有符号位必须相同；而字符的长度则可以不同
4. 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引。

```
父表
mysql> create table province(
    -> id smallint unsigned primary key auto_increment,
    -> pname varchar(20) not null
    -> );
mysql> show columns from province;
+-------+----------------------+------+-----+---------+----------------+
| Field | Type                 | Null | Key | Default | Extra          |
+-------+----------------------+------+-----+---------+----------------+
| id    | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
| pname | varchar(20)          | NO   |     | NULL    |                |
+-------+----------------------+------+-----+---------+----------------+


==========================
子表

mysql> create table users(
    -> id smallint unsigned primary key auto_increment,
    -> username varchar(10) not null,
    -> pid smallint unsigned,
    -> foreign key (pid) references province(id)
    -> );

mysql> show columns from users;
+----------+----------------------+------+-----+---------+----------------+
| Field    | Type                 | Null | Key | Default | Extra          |
+----------+----------------------+------+-----+---------+----------------+
| id       | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
| username | varchar(10)          | NO   |     | NULL    |                |
| pid      | smallint(5) unsigned | YES  | MUL | NULL    |                |
+----------+----------------------+------+-----+---------+----------------+
```

查看索引

```
mysql> show indexes from province\G;
*************************** 1. row ***************************
        Table: province
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
1 row in set (0.00 sec)

ERROR:
No query specified

mysql> show indexes from users\G;
*************************** 1. row ***************************
        Table: users
   Non_unique: 0
     Key_name: PRIMARY
 Seq_in_index: 1
  Column_name: id
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null:
   Index_type: BTREE
      Comment:
Index_comment:
*************************** 2. row ***************************
        Table: users
   Non_unique: 1
     Key_name: pid
 Seq_in_index: 1
  Column_name: pid
    Collation: A
  Cardinality: 0
     Sub_part: NULL
       Packed: NULL
         Null: YES
   Index_type: BTREE
      Comment:
Index_comment:
2 rows in set (0.00 sec)

ERROR:
No query specified
```


#### 三、外键约束的参照操作
1. CASCADE: 从父表删除或更新且自动删除或更新子表中匹配的行
2. SET NULL: 从父表删除或更新，并设置子表中的外键列为NULL。如果使用改选项，必须保证子表列没有指定 NOT NULL
3. RESTRICT: 拒绝对父表的删除或更新操作
4. NO ACTION: 标准SQL的关键字，在MySQL中与RESTRICT相同

```
1. 创建 有参数的 子表 
mysql> create table users1(
    -> id smallint unsigned primary key auto_increment,
    -> username varchar(10) not null,
    -> pid smallint unsigned,
    -> foreign key (pid) references province(id) on delete cascade
    -> );

2. 父表插入一些内容 
mysql> insert province(pname) values('A');
mysql> select * from province;
+----+-------+
| id | pname |
+----+-------+
|  1 | A     |
|  2 | B     |
|  3 | C     |
+----+-------+

3. users1 表中
mysql> insert users1(username, pid) values('Tom', 3);
Query OK, 1 row affected (0.00 sec)

mysql> insert users1(username, pid) values('John', 3);
Query OK, 1 row affected (0.01 sec)

mysql> insert users1(username, pid) values('rose', 1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from users1;
+----+----------+------+
| id | username | pid  |
+----+----------+------+
|  1 | Tom      |    3 |
|  2 | John     |    3 |
|  3 | rose     |    1 |
+----+----------+------+

4. 删除父表的一条记录
mysql> delete from province where id = 3;
Query OK, 1 row affected (0.02 sec)

mysql> select * from users1;
+----+----------+------+
| id | username | pid  |
+----+----------+------+
|  3 | rose     |    1 |
+----+----------+------+
```


#### 四、表级约束与列级约束
1. 对一个数据列建立的约束，称为列级约束
2. 对多个数据列建立的约束，称为表级约束
3. 列级约束既可以在列定义时声明，也可以在列定义后声明
4. 表级约束只能在列定义后声明

#### 五、修改数据表
数据表的修改：列的添加，列的删除，约束的添加，约束的删除  

##### 1) 添加单列  
`ALTER TABLE table_name ADD [COLUMN] column_name column_definition [FIRST|AFTER column_name]`  
`AFTER diy_column_name` 插入的列位于 跟随列名（diy_column_name）之后  
`FIRST` 插入的列位于 最前面  
alter table users1 add password varchar(32) not null after username;  

```
mysql> show columns from users1;
+----------+----------------------+------+-----+---------+----------------+
| Field    | Type                 | Null | Key | Default | Extra          |
+----------+----------------------+------+-----+---------+----------------+
| id       | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
| username | varchar(10)          | NO   |     | NULL    |                |
| pid      | smallint(5) unsigned | YES  | MUL | NULL    |                |
+----------+----------------------+------+-----+---------+----------------+
3 rows in set (0.00 sec)

mysql> alter table users1 add age tinyint unsigned not null default 10;
Query OK, 0 rows affected (0.07 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show columns from users1;
+----------+----------------------+------+-----+---------+----------------+
| Field    | Type                 | Null | Key | Default | Extra          |
+----------+----------------------+------+-----+---------+----------------+
| id       | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
| username | varchar(10)          | NO   |     | NULL    |                |
| pid      | smallint(5) unsigned | YES  | MUL | NULL    |                |
| age      | tinyint(3) unsigned  | NO   |     | 10      |                |
+----------+----------------------+------+-----+---------+----------------+
4 rows in set (0.00 sec)

mysql> alter table users1 add password varchar(32) not null after username;
Query OK, 0 rows affected (0.06 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> show columns from users1;
+----------+----------------------+------+-----+---------+----------------+
| Field    | Type                 | Null | Key | Default | Extra          |
+----------+----------------------+------+-----+---------+----------------+
| id       | smallint(5) unsigned | NO   | PRI | NULL    | auto_increment |
| username | varchar(10)          | NO   |     | NULL    |                |
| password | varchar(32)          | NO   |     | NULL    |                |
| pid      | smallint(5) unsigned | YES  | MUL | NULL    |                |
| age      | tinyint(3) unsigned  | NO   |     | 10      |                |
+----------+----------------------+------+-----+---------+----------------+
5 rows in set (0.00 sec)
```

##### 2) 添加多列 —— 不能指定位置关系，只能添加在下方
`ALTER TABLE table_name ADD [COLUMN] (column_name column_definition, ...)`  
alter table users1 add (hehe varchar(21) not null, haha varchar(22) not null);

##### 3) 删除列
`ALTER TABLE table_name drop [column] column_name`  
alter table users1 hehe;  
alter table users1 drop test, drop password; 删除多列


#### 六、修改数据表 －－ 添加约束
##### 1) 添加主键约束
`ALTER TABLE table_name ADD [CONSTRAINT [symbol]] PRIMARY KEY [index_type] (index_column_name, ...)`  
alter table users2 add constraint pk_user2_id primary key (id);

##### 2) 添加唯一约束
`ALTER TABLE table_name ADD [CONSTRAINT [symbol]] UNIQUE [INDEK|KEY] [index_name] [index_type] (index_column_name, ...)`  
alter table users2 add unique (username);

##### 3) 添加外键约束
`ALTER TABLE table_name ADD [CONSTRAINT [symbol]] FOREIGN KEY [index_name] (index_column_name, ...) references_definition`
alter table users2 add foreign key (pid) references province (id);

##### 4) 添加/删除默认约束
`ALTER TABLE table_name ALTER [COLUMN] column_name {SET DEFAULT literal | DROP DEFAULT}`  
alter table users2 alter age set default 15;  添加  
alter table users2 alter age drop default;  删除


#### 七、修改数据表 －－ 删除约束
##### 1) 删除主键约束
`ALTER TABLE table_name DROP PRIMARY KEY`  
lter table users2 drop primiary key;

##### 2) 删除唯一约束
`ALTER TABLE table_name DROP {INDEX|KEY} index_name`  
alter table users2 drop index username;

##### 3) 删除外键约束
`ALTER TABLE table_name drop FOREIGN KEY fk_symbol`


#### 八、修改数据表 －－ 修改列定义合更名数据表
##### 1) 修改列定义
`ALTER TABLE table_name MODIFY [COLUMN] column_name column_definition [FIRST|AFTER column_name]`  
alter table users2 modify id smallint unsigned not null first;

##### 2) 修改列名称
`ALTER TABLE table_name CHANGE [COLUMN] old_column_name new_column_name column_definition [FIRST|AFTER column_name]`  
alter table users2 change pid p_id tinyint unsigned not null;

##### 3) 数据表更名
方法1. `ALTER TABLE table_name RENAME [TO|AS] new_table_name`
方法2. `RENAME TABLE table_name TO new_table_name [, tbl_name2 TO new_tabl_name2]...`
