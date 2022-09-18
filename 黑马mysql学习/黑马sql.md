### 参考资料

> 视频链接： https://www.bilibili.com/video/BV1Kr4y1i7ru?p=2&share_source=copy_web&vd_source=6164cc1e15b15d47186e6ecfe12edef8

### SQL分类

<img src="images/sql_class.png">

### DDL

#### 1. 数据库操作

```sql
SHOW DATABASES;#查询所有数据库

SELECT DATABASE();#查询当前数据库

CREATE DATABASE 数据库名;#创建数据库

CREATE DATABASE [if not exists] itcast [DEFAUlT CHARSET 字符集] [COLLATE 排序规则];#创建数据库

DROP DATABASE [if exists] 数据库名字;#删除数据库

USE 数据库名;#使用数据库
```

#### 2. 表操作-查询

```sql
SHOW TABLES;#查询数据库所有表

DESC 表名;#查询表结构

SHOW CREATE TABLE 表名;#查询指定标的建表语句

CREATE TABLE 表名{
	字段1 字段1类型[COMMENT 字段1注释],
	字段2 字段2类型[COMMENT 字段2注释],
	... ...
}[COMMENT 表注释];

CREATE TABLE tb_user (
  `id` int(11) COMMENT '编号',
  `name` varchar(50) COMMENT '姓名',
  `age` int(11) COMMENT '年龄',
  `gender` varchar(1) COMMENT '性别'
) COMMENT='用户表'
```

#### 3. SQL数据类型

> 1. 数值类型

<img src="images/data_type.png">

> 2. 字符串类型

<img src="images/str_type.png">

> 3. 日期类型

<img src="images/date_type.png">

```sql
CREATE TABLE emp(
	id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '性别',
    gender char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证',
    entrydate date comment '入职时间'
) comment '员工表';
```

#### 4. 表操作-修改&删除

```sql
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];#添加字段

ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);#修改数据类型

ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];#修改字段名和字段类型

ALTER TABLE 表名 DROP 字段名#删除字段名

ALTER TABLE 表名 RENAME TO 新表名#修改表名

DROP TABLE [IF EXISTS] 表名;#删除表

TRUNCATE TABLE 表名;#删除表，并重新创建该表
```

