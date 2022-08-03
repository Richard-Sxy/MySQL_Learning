### Mysql学习视频

> https://www.bilibili.com/video/BV1iq4y1u7vj?p=16&share_source=copy_web&vd_source=6164cc1e15b15d47186e6ecfe12edef8

### 说明

>Mysql不严谨；
>
>养成写sql语句好习惯，下文皆为sql列子，需执行寻求其含义；
>
>字符和日期用单引号。

### 第三章 基本SELECT

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
#与关键字重名时使用
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

#### 课后练习

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


### 第四章 运算符

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

#### 课后练习

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

### 第五章 排序与分页

#### 排序

> 如果没有使用排序操作，默认情况下查询返回的数据是按照添加数据顺序显示的

```sql
#按照salary从高到低的顺序显示员工信息
#使用ORDER BY 对查询到的数据进行排序操作
#默认排序从低到高，升序ASC，降序DESC
#ORDER BY 后没有显示指明排序方式，默认按照升序排列
SELECT employee_id, last_name, salary FROM employees ORDER BY salary DESC;
SELECT employee_id, salary, salary * 12 annual_sal FROM employees ORDER BY annual_sal;

#列的别名只能在ORDER BY中使用，不能在WHERE中使用
SELECT employee_id, salary, salary * 12 annual_sal FROM employees WHERE annual_sal > 81600;#错误语法

#WHERE 需要声明在FROM后，ORDER BY前
SELECT employee_id, salary #再执行这句
FROM employees WHERE department_id IN (50, 60, 70) #先执行这句
ORDER BY department_id DESC; #最后执行这句

#多列排序
#二级排序，显示员工信息，按照department_id降序排列，department_id一样是按照salary的升序排列
SELECT employee_id, salary, department_id FROM employees ORDER BY department_id DESC, salary ASC;
```

#### 分页

```sql
#mysql使用limit实现数据的分页显示
#每页显示20条记录，此时显示第一页
SELECT employee_id, last_name FROM employees LIMIT 0,20;

#每页显示20条记录，此时显示第二页
SELECT employee_id, last_name FROM employees LIMIT 20,20;

#每页显示20条记录，此时显示第三页
SELECT employee_id, last_name FROM employees LIMIT 40,20;

#实际使用
#每页显示pageSize条记录，此时显示第pageNo页
#公式：LIMIT (pageNo-1) * pageSize, pageSize;

# WHERE ... ORDER BY ... LIMIT 声明顺序
SELECT employee_id, last_name, salary FROM employees WHERE salary > 6000 ORDER BY salary DESC LIMIT 0,10;

#LIMIT的格式(严格来说)：LIMIT 位置偏移量,条目数
#但LIMIT 0,条目数 等同于 LIMIT 条目数
SELECT employee_id, last_name, salary FROM employees WHERE salary > 6000 ORDER BY salary DESC LIMIT 10;

#表里有107条数据，我们只想要显示第32、33条数据怎么办呢
SELECT employee_id, last_name FROM employees LIMIT 31,2;

#Mysql 8.0新特性 LIMIT ... OFFSET ...
#表里有107条数据，我们只想要显示第32、33条数据怎么办呢
SELECT employee_id, last_name FROM employees LIMIT 2 OFFSET 31;

#查询员工表中工资最高的员工信息
SELECT employee_id, last_name, salary FROM employees ORDER BY salary DESC LIMIT ;

#LIMIT 可以在Mysql，PGSQL，MariaDB，SQLite等数据库中使用，表示分页
#不能使用在SQL Server，DB2，Oracle
```

#### 课后练习

* 查询员工的姓名、部门号、年薪，按年薪降序，按姓名升序显示

```sql
SELECT last_name, department_id, salary * 12 annual_salary FROM employees ORDER BY annual_salary DESC, last_name ASC;
```

* 选择工资不在8000 到17000的员工姓名、工资，按工资降序，显示第21到40位置的数据

```sql
SELECT last_name, salary 
FROM employees 
WHERE salary NOT BETWEEN 8000 AND 17000 
ORDER BY salary DESC 
LIMIT 20, 20;

SELECT last_name, salary 
FROM employees 
WHERE salary < 8000 OR salary > 17000
ORDER BY salary DESC 
LIMIT 20, 20;
```

* 查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序

```sql
SELECT employee_id, last_name, email, department_id 
FROM employees
WHERE email LIKE '%e%'
ORDER BY LENGTH(email) DESC, department_id ASC;
```

### 第六章 **多表**查询

#### 基本：表起别名、连接条件

```sql
#查询员工姓名和其所在部门
SELECT employee_id, department_name
FROM employees, departments; #错误，缺少连接条件

SELECT employee_id, department_name
FROM employees CROSS JOIN departments; #错误，缺少连接条件

SELECT employee_id, last_name, department_name
FROM employees, departments 
WHERE employees.department_id = departments.department_id;

#如果查询语句中出现了多个表中都存在的字段，则必须指明此字段所在表
SELECT employee_id, last_name, department_name, departments.department_id
FROM employees, departments 
WHERE employees.department_id = departments.department_id;

#建议：从sql优化的角度，建议多表查询时，每个字段前都指明其所在的表。
SELECT employee_id, last_name, departments.department_name, departments.department_id
FROM employees, departments 
WHERE employees.department_id = departments.department_id;

#可以给表起别名，在SELECT和WHERE中使用的别名，表起别名就必须用别名
SELECT employee_id, last_name, dept.department_name, dept.department_id
FROM employees emp, departments dept 
WHERE emp.department_id = dept.department_id;

#查询员工的employee_id, last_name, department_name, city
SELECT emp.employee_id, emp.last_name, dept.department_name, loc.city
FROM employees emp, departments dept, locations loc
WHERE emp.department_id = dept.department_id AND dept.location_id = loc.location_id;

#查询员工名为'Abel'的人在哪个城市工作
SELECT emp.employee_id, emp.last_name, dept.department_name, loc.city
FROM employees emp, departments dept, locations loc
WHERE emp.department_id = dept.department_id AND dept.location_id = loc.location_id AND last_name = 'Abel';

#如果有n个表实现多表查询，则需要n-1个连接条件
```

#### 等值连接、非等值连接

* 非等值连接

```sql
SELECT emp.last_name, emp.salary, jg.grade_level
FROM employees emp, job_grades jg
WHERE emp.salary BETWEEN jg.lowest_sal AND jg.highest_sal;

SELECT emp.last_name, emp.salary, jg.grade_level
FROM employees emp, job_grades jg
WHERE emp.salary <= jg.highest_sal AND emp.salary >= jg.lowest_sal;
```

#### 自连接、非自连接

* 自连接

```sql
#查询员工id，员工姓名及其管理者的id和姓名
SELECT emp.employee_id, emp.last_name, mag.employee_id manager_id, mag.last_name manager_name
FROM employees emp, employees mag
WHERE emp.manager_id = mag.employee_id;
```

#### 内连接、外连接

* 内连接

> 内连接：合并同一列的两个以上的表的行，结果集中不包含一个表与另一个表不匹配的行

```sql
#查询员工的employee_id, department_name
SELECT emp.employee_id, dept.department_name
FROM employees emp, departments dept
WHERE emp.department_id = dept.department_id;
#SQL92见上

#SQL99语法实现上述内连接
SELECT emp.last_name, dep.department_name
FROM employees emp JOIN departments dep
ON emp.department_id = dep.department_id;

#SQL99语法实现内连接
#查询员工的employee_id, last_name, department_name, city
SELECT emp.employee_id, emp.last_name, dept.department_name, loc.city
FROM employees emp INNER JOIN departments dept
ON emp.department_id = dept.department_id 
JOIN locations loc
ON dept.location_id = loc.location_id;#INNER可以省略，完整为INNER JOIN
```

* 外连接

> 外连接：合并具有同一列的两个以上的表的行，结果集中除了包含一个表与另一个表匹配的行之外，
> 还查询到了左表或右表中不匹配的行
>
> 外连接的分类：左外连接、右外连接、满外连接
>
> 1. 左外连接：两个表在连接过程中除了返回满足连接条件的行以外还返回左表中不满足条件的行
> 2. 右外连接：两个表在连接过程中除了返回满足连接条件的行以外还返回右表中不满足条件的行
> 3. 满外连接：返回左外连接、右外连接、内连接的结果

```sql
#查询所有员工的last_name, department_name
#有所有两字=>注意外连接
#SQL92标准实现左外连接，使用+，下列子Mysql不支持，会执行错误
SELECT emp.last_name, dep.department_name
FROM employees emp, departments dep
WHERE emp.department_id = dep.department_id(+);#左外连接

#SQL92标准实现右外连接，使用+，下列子Mysql不支持，会执行错误
SELECT emp.last_name, dep.department_name
FROM employees emp, departments dep
WHERE emp.department_id(+) = dep.department_id;#右外连接

#查询所有员工的last_name, department_name
#SQL99语法实现左外连接
SELECT emp.last_name, dep.department_name
FROM employees emp LEFT OUTER JOIN departments dep
ON emp.department_id = dep.department_id;#OUTER可以省略

#SQL99语法实现右外连接
SELECT emp.last_name, dep.department_name
FROM employees emp RIGHT JOIN departments dep
ON emp.department_id = dep.department_id;#OUTER可以省略

#满外连接
#SQL99 Mysql不支持下述满外连接
SELECT emp.last_name, dep.department_name
FROM employees emp FULL JOIN departments dep
ON emp.department_id = dep.department_id;#OUTER可以省略，会报错

#实现满外连接效果
```

#### SQL99实现7种JOIN操作

![](images\SQL99 7operations.png)

> UNION：会执行去重操作
> UNION ALL：不去重

* 中间图：内连接

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp INNER JOIN departments dept
ON emp.department_id = dept.department_id
```

* 左上图：左外连接

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
```

* 右上图：右外连接

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp RIGHT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
```

* 左中图

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
```

* 右中图

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp RIGHT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
```

* 左下图：满外连接

```sql
# 方式1：左上图 UNION ALL 右中图
SELECT emp.employee_id, dept.department_name
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
UNION ALL
SELECT emp.employee_id, dept.department_name
FROM employees emp RIGHT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
```

```sqlite
# 方式2：左中图 UNION ALL 右上图
SELECT emp.employee_id, dept.department_name
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
UNION ALL
SELECT emp.employee_id, dept.department_name
FROM employees emp RIGHT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
```

* 右下图：左中图 UNION ALL 右中图

```sql
SELECT emp.employee_id, dept.department_name
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
UNION ALL
SELECT emp.employee_id, dept.department_name
FROM employees emp RIGHT OUTER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE emp.department_id IS NULL
```

#### SQL99新特性

* 自然连接

```sql
#原始连接
SELECT emp.employee_id, emp.last_name, dept.department_name
FROM employees emp JOIN departments dept
ON emp.department_id = dept.department_id
AND emp.manager_id = dept.manager_id;

#自然连接，会自动查询两张表中，所有相同的字段，然后进行等值连接
SELECT emp.employee_id, emp.last_name, dept.department_name
FROM employees emp NATURAL JOIN departments dept
```

* USING连接使用

```sql
#原始连接
SELECT emp.employee_id, emp.last_name, dept.department_name
FROM employees emp JOIN departments dept
ON emp.department_id = dept.department_id

#USING连接使用
SELECT emp.employee_id, emp.last_name, dept.department_name
FROM employees emp JOIN departments dept
USING(department_id);
```

#### 课后练习

* 显示所有员工的姓名、部门号、部门名称

```sql
SELECT emp.last_name, emp.department_id, dept.department_name 
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id;
```

* 查询90号部门员工的job_id,和90号部门的location_id

```sql
SELECT emp.job_id, dept.location_id
FROM employees emp JOIN departments dept
ON emp.department_id = dept.department_id
WHERE dept.department_id = 90
```

* 选择所有有奖金的员工的last_name,department_name,location_id,city

```sql
SELECT emp.last_name, emp.commission_pct, dept.department_name, dept.location_id, loca.location_id, loca.city
FROM employees emp LEFT OUTER JOIN departments dept
ON emp.department_id = dept.department_id 
LEFT OUTER JOIN locations loca
ON dept.location_id = loca.location_id
WHERE emp.commission_pct IS NOT NULL;
```

* 选择city在Toronto工作的员工的last_name,job_id,department_id,department_name

```sql
#SQL99语法
SELECT emp.last_name, emp.job_id, emp.department_id, dept.department_name
FROM employees emp JOIN departments dept
ON emp.department_id = dept.department_id
JOIN locations loca
ON dept.location_id = loca.location_id
WHERE loca.city = 'Toronto';

#SQL92语法
SELECT emp.last_name, emp.job_id, emp.department_id, dept.department_name
FROM employees emp, departments dept, locations loca
WHERE emp.department_id = dept.department_id 
AND dept.location_id = loca.location_id
AND loca.city = 'Toronto';
```

* 查询员工所在的部门名称,部门地址,姓名,工作,工资,其中员工所在部门的部门名称为'Executive'

```sql
SELECT dept.department_name, loca.street_address, emp.last_name, emp.job_id, emp.salary
FROM departments dept LEFT OUTER JOIN employees emp
ON emp.department_id = dept.department_id
LEFT OUTER JOIN locations loca
ON dept.location_id = loca.location_id
WHERE dept.department_name = 'Executive';
```

* 选择指定员工的姓名，员工号，以及他的管理者的姓名和员工号，结果类似于下面的格式

```sql
SELECT emp1.last_name, emp1.employee_id, emp2.last_name, emp2.employee_id
FROM employees emp1 LEFT OUTER JOIN employees emp2
ON emp1.manager_id = emp2.employee_id;
```

* 查询哪些部门没有员工

```sql
SELECT dept.department_id, dept.department_name
FROm departments dept LEFT OUTER JOIN employees emp
ON dept.department_id = emp.department_id
WHERE emp.department_id IS NULL;

#本题也可以使用子查询
SELECT dept.department_id, dept.department_name
FROM departments dept
WHERE NOt EXISTS(
SELECT *
FROM employees emp
WHERE emp.department_id = dept.department_id
);
```

* 查询哪个城市没有部门

```sql
SELECT loca.city
FROM locations loca LEFT OUTER JOIN departments dept
ON loca.location_id = dept.location_id
WHERE dept.location_id IS NULL;
```

* 查询部门为Sales或IT的员工信息

```sql
SELECT emp.employee_id, emp.last_name, emp.salary, dept.department_name
FROM employees emp INNER JOIN departments dept
ON emp.department_id = dept.department_id
WHERE dept.department_name IN ('Sales', 'IT');
```