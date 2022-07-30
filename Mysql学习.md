### Mysql学习视频

> https://www.bilibili.com/video/BV1iq4y1u7vj?p=16&share_source=copy_web&vd_source=6164cc1e15b15d47186e6ecfe12edef8

### 说明

>Mysql不严谨，养成写sql语句好习惯，下文皆为sql列子，需执行寻求其含义。

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
#与关键字重名
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


### 第四章

#### 算术运算符

> +、-、* 、/（div）、%（mod）

* 加法与减法运算符

  ```sql
  #DUAL为虚拟表
  SELECT 100, 100 + 0, 100 - 0, 100 + 50, 100 + 50 - 30, 100 + 35.5, 100 - 35.5
  FROM DUAL;
  SELECT 100 + '1' FROM DUAL; # 在java语言中结果是：149，这里是101，因为'1'会隐式转换为数值1
  SELECT 100 + 'a' FROM DUAL;#此时将'a'看做0处理，因为隐式转换无法执行
  SELECT 100 + NULL FROM DUAL;#NULL参数运算，结果为NULL
  ```

* 乘除

  ```sql
  SELECT 100, 100 * 1, 100 * 1.0, 100 / 1.0, 100 / 2, 100 + 2 * 5 / 2, 100 / 3, 100 DIV 0 FROM DUAL;
  ```

* 取模运算

  ```sql
  SELECT 12 % 3, 12 % 5, 12 MOD -5, -12 % 5, -12 % -5, 12 % 13, 12 % 20 FROM DUAL;
  #查询员工id为偶数的员工信息
  SELECT employee_id, last_name, salary FROM employees WHERE employee_id % 2 = 0;
  ```

#### 比较运算符

> =, <=>, <>, !=, <, <=, >, >=

```sql
SELECT 1 = 2, 1 != 2 FROM DUAL;
SELECT 1 = 2, 1 != 2, 1 = '1', 1 = 'a', 0 = 'a' FROM DUAL;#字符串存在隐式转换，如果转换数值不成功，则看做为0

SELECT 'a' = 'a', 'ab' = 'ab', 'a' = 'b' FROM DUAL;#两边都是字符串时，按ANSI编码比较规则进行比较
SELECT 1 = NULL, NULL = NULL FROM DUAL;#只要有NULL参与判断，结果就为NULL

SELECT last_name, salary FROM employees WHERE salary = 6000;
SELECT last_name, salary FROM employees WHERE commission_pct = NULL;#此时执行不会有结果满足

#<=>与=的区别，是其可判断NULL，为NULL而生
SELECT 1 <=> 2, 1 <=> '1', 1 <=> 'a', 0 <=> 'a' FROM DUAL;
SELECT 1 <=> NULL, NULL <=> NULL FROM DUAL;

#查询commission_pct为NULL的数据有哪些
SELECT last_name, salary, commission_pct FROM employees WHERE commission_pct <=> NULL;

SELECT 3 <> 2, '4' <> NULL, '' != NULL, NULL != NULL FROM DUAL;#只要有NULL参与判断，结果就为NULL
```

> IS NULL, IS NOT NULL, ISNULL

```sql
#查询commission_pct为NULL的数据有哪些
SELECT last_name, salary, commission_pct FROM employees WHERE commission_pct IS NULL;
SELECT last_name, salary, commission_pct FROM employees WHERE ISNULL(commission_pct);

#查询commission_pct不为NULL的数据有哪些
SELECT last_name, salary, commission_pct FROM employees WHERE commission_pct IS NOT NULL;
SELECT last_name, salary, commission_pct FROM employees WHERE NOT commission_pct <=> NULL;
```

> LEAST()取最小，GREATEST()取最大，按字典序比较

```sql
SELECT LEAST('g', 'b', 't', 'm'), GREATEST('g', 'b', 't', 'm') FROM DUAL;

SELECT LEAST(first_name, last_name) FROM employees;
```

> BETWEEN 条件下界 AND 条件上界

```sql
#包含边界值
#查询工资在6000到8000的员工信息
SELECT employee_id, last_name, salary FROM employees WHERE salary BETWEEN 6000 AND 8000;
SELECT employee_id, last_name, salary FROM employees WHERE salary >= 6000 AND salary <= 8000;

#查询工资不在6000到8000的员工信息
SELECT employee_id, last_name, salary FROM employees WHERE salary < 6000 OR salary > 8000;
SELECT employee_id, last_name, salary FROM employees WHERE NOT salary BETWEEN 6000 AND 8000;
```

> IN (set), NOT IN (set)

```sql
#查询部门为10,20,30部门的员工信息
SELECT last_name, salary, department_id FROM employees WHERE department_id = 10 OR department_id = 20 OR department_id = 30;
SELECT last_name, salary, department_id FROM employees WHERE department_id IN (10, 20, 30);

#查询部门不是6000,7000,8000的员工信息
SELECT last_name, salary, department_id FROM employees WHERE salary NOT IN (6000, 7000, 8000);
```

> LIKE #模糊查询

```sql
#查询last_name中包含字符'a'的员工信息
# % 代表不确定个数的字符（0个，1个，多个）
SELECT last_name FROM employees WHERE last_name LIKE '%a%';

#查询last_name中以字符'a'开头的员工信息
SELECT last_name FROM employees WHERE last_name LIKE 'a%';


#查询last_name中包含字符'a'且包含字符'e'的员工信息
SELECT last_name FROM employees WHERE last_name LIKE '%a%' AND last_name LIKE '%e%';
SELECT last_name FROM employees WHERE last_name LIKE '%a%e%' OR last_name LIKE '%e%a%';

#_ 代表一个不确定的字符
#查询last_name中第二个字符是'a'的员工信息
SELECT last_name FROM employees WHERE last_name LIKE '_a%';

#查询last_name中第三个字符是'a'的员工信息
SELECT last_name FROM employees WHERE last_name LIKE '__a%';

#查询第二个字符是_且第三个字符是'a'的员工信息
#使用转义字符\
SELECT last_name FROM employees WHERE last_name LIKE '_\_a%';
#了解ESCAPE，指定相应符号为转义字符
SELECT last_name FROM employees WHERE last_name LIKE '_$_a%' ESCAPE '$';
```

> REGEXP,RLIKE#正则表达式

```sql
#正则表达式比较规则 百度
#^表示以一些字符开头
#$表示以一些字符结尾
SELECT 'shkstart' REGEXP '^shk', 'shkstart' REGEXP 't$', 'shkstart' REGEXP 'hk' FROM DUAL;
```

#### 逻辑运算符

> OR ||, AND &&, NOT !, XOR

```sql
SELECT last_name, salary, department_id FROM employees WHERE department_id = 10 OR department_id = 20;
SELECT last_name, salary, department_id FROM employees WHERE department_id = 10 AND department_id = 20;
SELECT last_name, salary, department_id FROM employees WHERE department_id = 50 AND salary > 6000;

SELECT employee_id, last_name, salary FROM employees WHERE NOT salary BETWEEN 6000 AND 8000;
SELECT employee_id, last_name, salary FROM employees WHERE !(salary BETWEEN 6000 AND 8000);

#2个条件只满足其1而另一个不满足
SELECT last_name, salary, department_id FROM employees WHERE department_id = 50 XOR salary > 6000;
#AND优先级高于OR
```

#### 位运算

> &, |, ^, ~, >>, <<

```sql
SELECT 1 & 1, 12 | 5, 12 ^ 5 FROM DUAL;
SELECT 4 >> 1, 4 << 1, 5 >> 1 FROM DUAL
SELECT 10 & ~1 FROM DUAL;
```

#### 第四章课后练习

* 选择工资不在5000到12000的员工的姓名和工资

```sql
SELECT last_name, salary FROM employees WHERE NOT salary BETWEEN 5000 AND 12000;
SELECT last_name, salary FROM employees WHERE salary < 5000 OR salary > 12000;
```

* 选择在20或50号部门工作的员工姓名和部门号

```sql
SELECT last_name, department_id FROM employees WHERE department_id IN (20, 50);
SELECT last_name, department_id FROM employees WHERE department_id = 20 OR department_id = 50;
```

* 选择公司中没有管理者的员工姓名及job_id

```sql
SELECT last_name, job_id, manager_id FROM employees WHERE manager_id IS NULL;
SELECT last_name, job_id, manager_id FROM employees WHERE manager_id <=> NULL;
```

* 选择公司中有奖金的员工姓名，工资和奖金级别

```sql
SELECT last_name, salary, commission_pct FROM employees WHERE commission_pct IS NOT NULL;
SELECT last_name, salary, commission_pct FROM employees WHERE NOT commission_pct <=> NULL;
```

* 选择员工姓名的第三个字母是a的员工姓名

```sql
SELECT last_name FROM employees WHERE last_name LIKE '__a%';
```

* 显示出表employees表中first_name以'e'结尾的员工信息

```sql
SELECT first_name, last_name FROM employees WHERE first_name LIKE '%e';
SELECT first_name, last_name FROM employees WHERE first_name REGEXP 'e$';
#first_name以e开头的
SELECT first_name, last_name FROM employees WHERE first_name REGEXP '^e';
```

* 选择姓名中有字母a和k的员工姓名

```sql
SELECT last_name FROM employees WHERE last_name LIKE '%a%k%' OR last_name LIKE '%k%a%';
SELECT last_name FROM employees WHERE last_name LIKE '%a%' AND last_name LIKE '%k%';
```

* 显示出表employees部门编号在80-100之间的姓名、工种

```sql
SELECT last_name, job_id, department_id FROM employees WHERE department_id >= 80 AND department_id <= 100;
SELECT last_name, job_id, department_id FROM employees WHERE department_id BETWEEN 80 AND 100;
```

* 显示出表employees的manager_id是100,101,110的员工姓名、工资、管理者id

```sql
SELECT last_name, salary, manager_id FROM employees WHERE manager_id IN (100, 101, 110);
```

