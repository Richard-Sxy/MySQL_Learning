

### 参考资料

> 视频链接： https://www.bilibili.com/video/BV1Kr4y1i7ru?p=2&share_source=copy_web&vd_source=6164cc1e15b15d47186e6ecfe12edef8

### SQL分类

<img src="images/sql_class.png">

### DDL

> 数据库定义语言：用来定义数据库、表、字段

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

### DML

> 数据操作语言：对数据表的记录进行增、删、改

#### 1. 插入

```sql
1.给指定字段添加数据
INSERT INTO 表名(字段名1, 字段名2, ......) VALUES (值1, 值2, ......);

INSERT INTO employee(id, workno, name, gender, age, idcard, entrydate) VALUES (1, '1', 'Itcast', '男', 10, '123412341234123412', '2022-09-18');

2.给全部字段添加数据
INSERT INTO 表名 VALUES(值1,值2,...);

INSERT INTO employee VALUES (2, '2', 'Itcast', '男', 10, '123412348234123412', '2022-09-17');

3.批量添加数据
INSERT INTO 表名(字段1,字段2,......) VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);
INSERT INTO 表名 VALUES(值1,值2,...),(值1,值2,...),(值1,值2,...);
```

#### 2. 更新和删除

```sql
1. 更新
UPDATE 表名 SET 字段1=值1,字段2=值2,...[WHERE 条件];

UPDATE employee SET name='itheima' WHERE id = 1;
UPDATE employee SET name='xiaoming',gender='女' WHERE id = 1;
UPDATE employee SET entrydate = '2008-01-01';#修改所有数据的日期为2008-01-01

2. 删除
DELETE FROM 表名 [WHERE 条件]

DELETE FROM employee WHERE gender = '女';
DELETE FROM employee;#删除该表所有数据
```

### DQL

> 数据查询语言：查询数据库中表的记录
>
> 见readme.md

### DCL

> 数据库控制语言：管理数据库用户、控制数据库的访问权限

#### 1. 管理用户

```sql
1. 查询用户
USE mysql;
SELECT * FROM user;

2. 创建用户
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';

CREATE USER 'xiaomi'@'localhost' IDENTIFIED BY '123456asd...';

3. 修改用户密码
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';

ALTER user 'xiaomi'@'localhost' IDENTIFIED WITH mysql_native_password by '123456Asd1231...';

4. 删除用户
DROP USER '用户名'@'主机名';

DROP user 'xiaomi'@'localhost';
```

#### 2. 权限管理

<img src="images/dcl_privileges.png">

```sql
1. 查询权限
SHOW GRANTS FOR '用户名'@'主机名';

SHOW GRANTS FOR 'xiaomi'@'localhost';

2. 授予权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';

GRANT ALL ON itcast.* TO 'xiaomi'@'localhost';#赋予用户访问itcast数据库所有表的权限

3. 撤销权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';

REVOKE ALL ON itcast.* FROM 'xiaomi'@'localhost';
```

### 约束

#### 1. 常见约束

<img src="images/sql_cons.png">

```sql
#列子
CREATE TABLE user(
	id int PRIMARY KEY AUTO_INCREMENT comment '主键',
	name varchar(10) NOT NULL UNIQUE comment '姓名',
	age int comment '年龄' CHECK(age > 0 && age <= 120),#此句不生效，可能跟mysql版本有关
	status char(1) DEFAULT '1' comment '状态',
	gender char(1) comment '性别'
) comment '用户表';

INSERT INTO user(name,age,status,gender) VALUES ('tom1',19,'1','男'), ('tom2',20,'1','男');
```

#### 2. 外键约束

> 外键用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性

```sql
CREATE TABLE 表名(
    字段名 字段类型,
    ...
    [CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);

#列子
CREATE TABLE dept(
	dept_id int PRIMARY KEY comment '部门id',
	name varchar(10) UNIQUE comment '部门名字'
)COMMENT '部门表'; 
INSERT INTO dept VALUES(1, '1号'),(2, '2号');
INSERT INTO employee VALUES (2, '2', 'Itcast', '男', 10, '123412348234123412', '2022-09-17', '1');
INSERT INTO employee VALUES (1, '1', 'xiaozhu', '女', 10, '123412348234123423', '2022-09-15', '2');
ALTER TABLE employee add CONSTRAINT fk_emp_dept_id FOREIGN KEY(dept_id) REFERENCES dept(dept_id);#添加外键
ALTER TABLE employee DROP FOREIGN KEY fk_emp_dept_id;#删除外键
```

#### 3. 外键删除/更新行为

<img src="images/sql_foreign_key_beha.png">

```sql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;

#列子1
ALTER TABLE employee add CONSTRAINT fk_emp_dept_id FOREIGN KEY(dept_id) REFERENCES dept(dept_id) ON UPDATE CASCADE ON DELETE CASCADE;
#列子2
ALTER TABLE employee DROP FOREIGN KEY fk_emp_dept_id;#删除外键
ALTER TABLE employee add CONSTRAINT fk_emp_dept_id FOREIGN KEY(dept_id) REFERENCES dept(dept_id) ON UPDATE SET NULL ON DELETE SET NULL;
```

