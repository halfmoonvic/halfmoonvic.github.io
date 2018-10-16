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

1. NOT NULL 不许为空
2. unique key 唯一约束
3. primary key 主键　　
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


