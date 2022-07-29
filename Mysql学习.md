#### Mysql学习视频

> https://www.bilibili.com/video/BV1iq4y1u7vj?p=16&share_source=copy_web&vd_source=6164cc1e15b15d47186e6ecfe12edef8

#### 说明

>Mysql不严谨，养成写sql语句好习惯

### 第三章

#### SELECT使用

```sql
SELECT * FROM employees;
SELECT employee_id, last_name, salary FROM employees;
```

#### 别名使用

```sql
#as->alias
#列的别名可以使用一对""引起来
SELECT employee_id AS emp_id, last_name lname, department_id "dept_id", salary * 12 annual_sal
FROM employees;
```

#### 去除重复行

```sql
#查询员工表一共有哪些部门id
SELECT DISTINCT department_id FROM employees
```

#### 空值参与运算

```sql
#NULL不等同于0, '', 'null'
SELECT * FROM employees;
#空值参与运算结果也为NULL
SELECT employee_id, salary "月工资", salary * (1 + commission_pct) * 12 "年工资" FROM employees;
```

#### 着重号``

```sql
SELECT * FROM order;#错误
SELECT * FROM `order`;
```

#### 查询常数

```sql
SELECT '尚硅谷', employee_id, last_name FROM employees;
```

#### 显示表结构

```sql
DESCRIBE employees;#显示了表中字段的详细信息
DESC employees;#显示了表中字段的详细信息
DESC departments;
```

#### 过滤数据WHERE

```sql
#WHERE一定要放FROM后面且仅挨着FROM
#查询90号部门的员工信息
SELECT * FROM employees WHERE department_id = 90;
#查询last_name为'King'的员工信息
SELECT * FROM employees WHERE last_name = 'King';
```

#### 第三章课后练习

* 查询员工12个月的工资总和，并起别名为ANNUAL SALARY

  ```sql
  SELECT employee_id, last_name, salary * 12 "ANNUAL SALARY"
  FROM employees;
  #加了奖金
  SELECT employee_id, last_name, salary * 12 * (1 + IFNULL(commission_pct, 0)) "ANNUAL SALARY"
  FROM employees;
  ```

* 查询employees表中去除重复的job_id以后的数据

  ```sql
  SELECT DISTINCT job_id FROM employees;
  ```

* 查询工资大于12000的员工姓名和工资

  ```sql
  SELECT last_name, salary FROM employees WHERE salary > 12000;
  ```

* 查询员工号为176的员工的姓名和部门号

  ```sql
  SELECT last_name, department_id FROM employees WHERE employee_id = 176;
  ```

* 显示表departments的结构，并查询其中的全部数据

  ```sql
  DESCRIBE departments;
  DESC departments;
  SELECT * FROM departments;
  ```

  