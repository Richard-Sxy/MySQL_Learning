## 运维篇

### 日志

#### 错误日志

错误日志是MySQL中最重要的日志之一，它记录了当mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，建议首先查看此日志。
该日志是默认开启的，默认存放目录/var/log/，默认的日志文件名为mysqld.log 。查看日志位置:

```sql
show variables like '%log_error%'
```

#### 二进制日志

二进制日志(BINLOG)记录了所有的DDL ( 数据定义语言)语句和DML (数据操纵语言)语句，但不包括数据查询(SELECT、 SHOW)语句。

**作用：**

- 灾难时的数据恢复;

- MySQL的主从复制。

在MySQL8版本中，默认二进制日志是开启着的，涉及到的参数如下:

```sql
show variables like '%log_bin%'
```

开启方法：https://blog.csdn.net/zwrlj527/article/details/93497918

**日志格式：**

MySQL服务器中提供了多种格式来记录进制日志，具体格式及特点如下:

| 日志格式  | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| STATEMENT | 基于SQL语句的日志记录，记录的是SQL语句，对数据进行修改的SQL都会记录在日志文件中。 |
| ROW       | 基于行的日志记录，记录的是每一行的数据变更。(默认)           |
| MIXED     | 混合了STATEMENT和ROW两种格式，默认采用STATEMENT，在某些特殊情况下会自动切换为ROW进行记录。 |

```sql
show variables like '%binlog_format%';
```

**日志查看**

由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具mysqlbinlog来查看，具体语法:

```sql
mysqlbinlog -V 文件名 #基于行的日志记录
mysqlbinlog 文件名 #基于SQL语句的日志记录
```

**日志删除**

对于比较繁忙的业务系统，每天生成的binlog数据巨大，如果长时间不清除，将会占用大量磁盘空间。可以通过以下几种方式清理日志:

| 指令                                            | 含义                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| reset master                                    | 删除全部binlog日志，删除之后，日志编号，将从binlog.000001重新开始 |
| purge master logs to 'binlig.*********'         | 删除*****编号之前的所有日志                                  |
| purge master logs before 'yyy-mm-dd hh24:mi:ss' | 删除日志为"yyy-mm-dd hh24:mi:ss"之前产生的所有日志           |

也可以在mysql的配置文件中配置二进制日志的过期时间，设置了之后，二进制日志过期会自动删除。

```sql
show variables like '%binlog_expire_logs_seconds%';
show variables like '%expire_logs%';#低版本查询
```

#### 查询日志

查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。默认情况下，查询日志是未开启的。如果需要开启查询日志，可以设置以下配置:

```sql
show variables like '%general%'

#修改MySQL的配置文件/etc/my.cnf文件，添加如下内容:
#该选项用来开启查询日志，可选值: 0或者1 ; 0代表关闭，1代表开启
general_log=1
#设置日志的文件名，如果没有指定，默认的文件名为host_name.log
general_log_file=mysql_query.log
```

#### 慢查询日志

慢查询日志记录了所有执行时间超过参数long_query_time 设置值并且扫描记录数不小min_examined_row_limit
的所有的SQL语句的日志，默认未开启。long_query_ time 默认为10秒，最小为0，精度可以到微秒。

```sql
#慢查询日志
slow_query_log=1
#执行时间参数
long_query_time=2
```

默认情况下，不会记录管理语句，也不会记录不使用索引进行查找的查询。可以使用log_slow_admin_statements和更改此行为log_queries_not_using_indexes，如下所述。

```sql
#记录执行较慢的管理语句
log_slow_admin_statements =1 
#记录执行较慢的未使用索引的语句
log_queries_not_using_indexes = 1
```

### 主从复制

主从复制是指将主数据库（Master）的DDL和DML操作通过二进制日志传到从库服务器中，然后在从库（Slave）上对这些日志重新执行(也叫重做)，从而使得从库和主库的数据保持同步。

MySQL支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制。

MySQL复制的**优点**主要包含以下三个方面:

- 主库出现问题，可以快速切换到从库提供服务。
- 实现读写分离，降低主库的访问压力。
- 可以在从库中执行备份，以避免备份期间影响主库服务。

#### 原理

<img src="images/images_20221012104118.png">

从上图来看，复制分成三步:
1. Master主库在事务提交时，会把数据变更记录在二进制日志文件Binlog中。
2. 从库读取主库的二进制日志文件Binlog，写入到从库的中继日志Relay Log。
3. Slave重做中继日志中的事件，将改变反映它自己的数据。

#### 搭建

**主库配置**

```sql
1.修改配置文件/etc/my.cnf

#mysql服务ID，保证整个集群环境中唯一，取值范围: 1 ~ 2^32-1，默认为1
server-id=1
#是否只读，1代表只读，0代表读写
read-only=0
#忽略的数据，指不需要同步的数据库
#binlog-ignore-db=mysql
#指定同步的数据库
#binlog-do-db=db01

2.重启数据库
systemctl restart mysqld

3.登录mysql，创建远程连接的账号，并授予主从复制权限
#创建itcast用户，并设置密码，该用户可在任意主机连接该MySQL服务
CREATE USER 'itcast'@'%' IDENTIFIED WITH mysql_native_password BY 'Root@123456';
#为'itcast'@'%'用户分配主从复制权限
GRANT REPLICATION SLAVE ON *.* TO 'itcast'@'%';

4.通过指令，查看二进制日志坐标，
show master status;

字段含义说明:
file :从哪个日志文件开始推送日志文件
position :从哪个位置开始推送日志
binlog_ignore_db :指定不需要同步的数据库
```

**从库配置**

```sql
1.修改配置文件/etc/my.cnf
#mysql服务ID，保证整个集群环境中唯一，取值范围: 1 ~ 2^32-1，默认为1
server-id=2
#是否只读，1代表只读，0代表读写(代表普通用户) super-read-only=1(超级用户)
read-only=1

2.重启数据库
systemctl restart mysqld

3.登录mysql,设置主库配置
CHANGE REPLICATION SOURCE TO
SOURCE_HOST='xxx.xxx',SOURCE_USER='xxx',SOURCE_PASSWORD='xxx',SOURCE_LOG_FILE='xxx', SOURCE_LOG_POS=xxx;
上述是8.0.23中的语法。如果mysql是8.0.23之前的版本，执行如下SQL: 
CHANGE MASTER TO MASTER_HOT='xxx.xxx.xxx.xxx', MASTER_USER='xxx',MASTER_PASSWORD='xxx', MASTER_LOG_FlLE='xxx',MASTER_LOG_POS=xxx;

#列
CHANGE REPLICATION SOURCE TO
SOURCE_HOST='192.168.200.200',SOURCE_USER='itcast',SOURCE_PASSWORD='Root@123456',SOURCE_LOG_FILE='binlog.000004',SOURCE_LOG_POS=663;

4.开启同步操作
start replica; #8.0.22之后
start slave; #8.0.22之前
```

#### 测试

```sql
#1.在主库上创建数据库、表，并插入数据
create database db01;
use db01;

create table tb user(
	id int(11) primary key not null auto_increment,
	name varchar(50) not nul,
	sex varchar(1)
) engine=innodb default charset=utf8mb4;

insert into tb_user(id,name,sex) 
values(null,'Tom','1'),(null,'Trigger','0'),(nll,'Dawn','1');
```

