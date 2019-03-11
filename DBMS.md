# DBMS

## 数据库管理系统(DBMS):

### RDBMS:关系型数据库管理系统：

C/S架构：通过专用协议通信，由设计数据库管理系统的程序员提供；

关系模型： 表(行，列)，二维关系，二维关系不仅存在于表内，也存在于表间；

#### 设计范式：

* 第一范式： 确保每列保持原子性；

        数据库表中的所有字段都是不可分解的原子值。

        比如某些数据库系统中需要用到“地址”这个属性，本来直接将“地址”属性设计成一个数据库表的字段就行。

        但是如果系统经常会访问“地址”属性中的“城市”部分，那么就非要将“地址”这个属性重新拆分为省份、城市、详细地址等多个部分进行存储，这样在对地址中某一部分操作的时候将非常方便。
        这样设计才算满足了数据库的第一范式；

* 第二范式： 确保表中的每列都和主键相关，而不能只与主键的某一部分相关（主要针对联合主键而言）。

        也就是说在一个数据库表中，一个表中只能保存一种数据，不可以把多种数据保存在同一张数据库表中。

* 第三范式： 确保数据表中的每一列数据都和主键直接相关，而不能间接相关；


#### 关系运算：

* 选择： 从一个表中挑选出符合条件的行；
* 投影： 挑选出符合条件的列；

#### 数据库(组成部分)：

* 表，视图(虚表)
* 索引： 索引将提高查询进度，但会降低写入速度(写入后需要重建索引)
* SQL： Structure Query Language(结构化查询语言)：

        DDL：数据定义语言；
        DML：数据操作语言；

        SQL编程接口：
            存储过程： 无返回值的函数；
            存储函数： 有返回结果的代码片段；
            触发器： 当某个特定的事件发生时，触发器会执行实现存储的某些语句；
            事件调度器： 类似于Linux Cron Table；
            过程式编程： 选择、循环等。


#### 三层模型(底层角度分析)：

* 物理层： 存储在表空间中； 基于 插件式 存储引擎提供；
* 逻辑层： 表、索引、视图，数据库内奸组件；
* 视图层： 用户最终看到的样子；可能是整个数据集中很有限的其中一部分；

#### RDBMS解决方案：

* Sybase： 已迷失方向；
* Infomix：已迷失方向；

* 商业解决方案：
  - Oracle
  - IBM DB2
  - MS-SQLServer

* MySQL，MariaDB， 演进历史： MySQL AB公司 --> SUN --> Oracle
* PostgreSQL,也称PGSQL；
* SQLite： 简化的关系型数据库引擎，存储在文件系统上，只是一个库，嵌入式的解决方案；比如Android智能机上的通讯录的管理；

    MySQL版本(由Oracle维护)： --> 5.1 --> 5.5 --> 5.6 --> 5.7

* MariaDB：
  - 采用MySQL新特性，整合社区力量开发；
  - 和MySQL兼容，调用API一致；
  - 插件式存储引擎：
    * 能支持众多的存储引擎，也可自行开发存储引擎； SHOW ENGINS;
  - 单进程多线程模型： 连接线程 + 守护线程

---

#### 配置文件：

* 集中式的配置(能够为mysql的各应用程序提供配置信息):
  - [mysqld] ： mysqld服务应用程序
  - [mysqld_safe] : 线程安全
  - [mysqld_multi] ： 多实例安全；
  - [server] : mysql服务器端程序都有效；
  - [mysql] ： mysql客户端配置
  - [mysqldump] ： mysql备份工具的配置；
  - [client] : 
  - 格式： parameter = value —— e.g : skip-name-resolve 跳过名称解析；

* 配置文件查找路径和顺序：
  - 1. /etc/my.cnf
  - 2. /etc/mysql/my.cnf
  - 3. /$MYSQL_HOME/my.cnf
  - 4. --default-extra-file=/path/to/somedir/my.cnf
  - 5. ~/.my.cnf

    不论前面的是否存在，都会查找后面的文件；
    重复出现的参数，后面加载的生效；

    参数之间用"_" 或 "-" 连接
    skip_name_resolve    skip-name-resolve

* 安装方法：
  - os vendor：
        rpm -ivh package, 使用场景： 必须依赖OS时，购买了RHEL的服务时；
  - MySQL官方提供：

        官方rpm package
        通用二进制格式： 展开即可用； 不太适合于大规模部署；
        源码编译安装： 建议做成包管理器的方式进行；

* 安装后的设定：
  - 1. 为所有root用户设定密码：

        mysql> SET PASSWORD

        mysql> update mysql.user SET password=PASSWORD('your_pass') WHERE name=root;
        mysql> FLUSH PRIVILEGES;

        # mysqladmin

  - 2. 删除所有匿名用户： mysql> DROP USER ''@'localhost';
  - 3. 建议关闭主机名反解功能： skip-name-resolve

        MySQL元数据数据库： mysql库(user, host, db等)

* mysql --> mysqld :
  - 客户端程序：
    * mysql： 交互式的CLI工具；
    * mysqldump： 数据导出工具(备份工具)，基于mysql协议向mysqld发起查询请求，并将查得的所有数据转换成insert等写操作语句保存在文本文件中； 从而完成数据库备份；
    * mysqladmin： 基于mysql协议管理mysqld；
    * mysqlimport： 数据导入工具；
  - 非客户端类的管理工具：不能远程实现，只能在mysqld节点上使用
    * myisamchk： 检测；
    * myisampack：打包；
  - 如何获取程序默认使用的配置：
    * mysql --print-defaults
    * mysqld --print-defaults
  - 客户端类应用程序的可用选项：
    * -u, --user= : 致命炼乳数据库使用的用户，缺省为root；
    * -h, --host= : 缺省为：localhost
    * -p, --password= : 密码
    * -P, --port= : 端口
    * --protocol={tcp|sock} :　连入数据库时使用的协议；
    * -S, --socket= : 指定套接字文件位置： 仅本机通信时；即-h为localhost或127.0.0.1时；
    * -D, --database= : 连接后默认切换为某数据库；
    * -C, --compress :数据的传入传出是否压缩；
    * -e ： mysql -e "SQL-Statement",不连接数据库，仅执行SQL语句；例如： # mysql -e "SHOW DATABASES;"
  - mysql的使用模式： 
    * 交互式模式： 可运行命令有两类：
      - 客户端命令： \h, help 无需语句结束符；
      - 服务器端命令： SQL语句，需要语句结束符；
    * 脚本模式：
      - [root@localhost /]# mysql -uUSERNAME -hHOST -pPASSWORD < /path/from/somefile.sql
      - mysql> source /path/from/somefile.sql
  - 服务器端(mysqld): 工作特性有多种定义方式
    * 命令行选项：
      - 启动时使用 mysqld --options
        * 查看： # mysqld --verbose --help
        * 查看： # mysql --help --verbose
    * 配置文件参数：
      - 获取可用参数列表： # msyqld --help --verbose 
    * 获取运行中的mysql进程使用的各参数(变量)及其值：
      - mysql> SHOW GLOBAL VARIABLES; 全局变量，需要管理员权限；
      - mysql> SHOW SESSION VARIABLES; 当前会话变量；
      - 注意：其中有些参数支持运行时修改，会立即生效，有些参数不支持，且只能通过修改配置文件，并重启服务器程序生效；
      - 有些参数的作用域是全局的，且不可改变，有些可以为每个用户提供单独的配置；
    * 修改服务器变量的值：不能永久有效
      - mysql> help SET
      - 全局：
        * mysql> SET GLOBAL system_var_name=value;
        * mysql> SET @@global.system_var_name=value;
      - 会话：
        * mysql> SET [SESSION] system_var_name=value;
        * mysql> SET @@[session.]system_var_name=value;
    * 状态变量：
      - 用于保存mysqld运行中的统计数据的变量；
      - mysql> SHOW BLOBAL STATUS;
      - mysql> SHOW [SESSION] STATUS;

* SQL接口： ANSI定义了SQL标准(ANSI SQL)
  - SQL-86,SQL-89,SQL-92,SQL-99,SQL-03
  - 大多数遵循到中等级别；
  - 对于标准遵守得越严格，灵活度越低；

---

### MySQL数据类型：

#### 字符型
* 定长数据类型：分配固定长度的空间；
  - CHAR： 不区分字符大小写；
  - BINARY： 区分字符大小写；
* 变长数据类型：需要结束符，结束符至少需要占据一个自己；
  - VARCHAR： 不区分字符大小写；
  - VARBINARY： 区分字符大小写；
* TEXT：不区分字母大小写；
  - TINYTEXT： 2^8
  - TEXT: 2^16
  - MEDIUMTEXT: 2^24
  - LONGTEXT: 2^32
* BLOB:
  - TINYBLOB(2^8)
  - BLOB(2^16)
  - MEDIUMBLOB(2^24)
  - LOGBLOB(2^32)
* ENUM、SET :　枚举、集合

#### 数值型
* 精确数值型：
  - 整型：
    * TINYINT： 0 - 2^8-1 (但还要考虑是否有符号)
    * SMALLINT： 0 - 2^16-1
    * MEDIUMINT: 0 - 2^24-1
    * INT: 0 - 2^32-1
    * BIGINT: 0 - 2^64-1
  - 十进制型：
    * DECIMAL： 在金融统计中要求精确数值统计；
* 近似数值型：
  - 浮点型：
    * FLOAT： 单精度浮点型；
    * DOUBLE： 双精度浮点型；
  - BIT： A Bit Filed

#### 日期时间型
* DATE： 占3个字节
* TIME： 占3个字节
* DATETIME： 8个字节，额外标明date和time的分割位；
* TIMESTAMP： 4个字节，时间戳计时法；
* YEAR(2): 00-99, 1 byte;
* YEAR(4): 1915-2155, 1 byte;

#### 内建类型
* ENUM: 枚举，从给定的字符串中选一个字符串，可以枚举1-65535个字符；
* SET： 集合，可以存储最多64位的二进制字符的集合；从中选一个或多个；

#### 字符类型修饰符
* NOT NULL： 非空约束，要求必须填入字符；
* NULL： 允许为空，默认就是如此；
* DEFAULT 'STRING' : 指明默认值；
* CHARACTER SET '' : 使用的字符集； mysql> SHOW CHARACTER SET;
* COLLATION： 使用的排序规则： mysql> SHOW COLLATION;
#### 整型数据修饰符
* NOT NULL
* NULL
* DEFAULT NUMBER
* AUTO_INCREMENT，自动增长，前提是：
  - UNSIGNED，无符号；
  - PRIMARY KEY | UNIQUE KEY
  - NOT NULL
  - 查看上一次插入时的自动增长的ID的值；
#### 日期时间修饰符
* NOT NULL
* NULL
* DEFAULT
#### 内建类型SET和ENUM的修饰符
* NOT NULL
* NULL
* DEFAULT

---

### SQLMODE：
* 定义mysql对约束等的响应行为；
* 修改GLOBAL sql_mode：

        mysql> SET GLOBAL sql_mode='MODE';
        mysql> SET @@global.sql_mode='MODE';
        mysql> SHOW GLOBAL VARIABLES LIKE 'sql_mode';

    需要修改权限： 仅对修改后新创建的会话有效，对已经建立的会话无效；

* 修改SESSION sql_mode:

        mysql> SET SESSION sql_mode='MODE';
        mysql> SET @@session.sql_mode='MODE';

* 使用选项： --sql-mode="MODE" 启动mysqld

* 常用MODE：
  - 为空： 
  
        不执行严格的检查，如果要插入的字段长度超过列定义的长度，那么mysql不会终止操作，而是会自动截断后面的字符继续插入操作；
  - TRADITIONAL： 

        SET sql_mode='TRADITIONAL";
        当在列中插入不正确的值时"给出错误而不是警告"。注释：一旦发现错误理解放弃INSERT/UPDATE 。
  - STRICT_TRANS_TABLES: 

        如果不能将给定的值插入到事务表中，则放弃该语句；
        对于非事务表，如果值出现在单行语句或多行语句的第一行，则放弃该语句。
  - STRICT_ALL_TABLES:

---

### SQL: DDL,DML

#### DDL:数据定义语言
#### DML:数据操作语言
#### 数据库可执行的操作
#### 表：二维关系

### MySQL基础

#### mysql Arch
#### 单进程多线程模型
#### MySQL数据文件类型
#### DDL & DML
* 索引
* 视图： VIEW，虚表
* DML：
  - INSERT
  - DELETE
  - UPDATE
  - SELECT

---

### MySQL存储引擎(插件式存储引擎)

存储引擎也被成为：表类型；　

CREATE TABLE ... ENGINE=

#### InnoDB(支持事务)

#### MyISAM(不支持事务)

#### 其他存储引擎

#### MariaDB支持的其他存储引擎


---

### 并发控制

#### 锁
#### 锁粒度
#### 分类

---

### 编译安装MySQL-5.5


---

### MySQL事务：

#### 事务
#### ACID测试
#### 事务
#### 事务支持savepoint
#### 事务隔离级别
#### MVCC：过版本并发控制
#### 死锁
#### 事务日志

---

### MySQL用户和权限管理

#### 权限类别
* 库级别
* 表级别
* 字段级别
* 管理类
* 程序类
* 数据操作
* 所有权限

---

#### 元数据数据库

---

#### 用户账号：
* 用户组成部分
* 创建用户
* 用户重命名
* 删除用户
* 修改密码
* 忘记管理员密码的解决办法
* 授权:GRANT
* 取消授权:REVOKE
* 查询缓存

---

### MySQL中的索引

#### 基本法则
#### 索引类型
* B+ Tree索引
* Hash索引
* 空间索引:(R-Tree)
* 全文索引(FULLTEXT)

#### 索引优点
#### 高性能索引策略
#### 冗余和重复索引
#### 通过EXPLAIN来分析索引的有效性

---

### CentOS7编译安装MariaDB

---

### MariaDB日志

#### 1.查询日志: query log
#### 2.慢查询日志: slow query log
#### 3.错误日志: error log
#### 4.二进制日志: binary log
#### 5.中继日志： reley log
#### 6.事务日志： transaction log

---

### 备份恢复

#### 1.为什么要备份？
#### 2.要注意的要点：
#### 备份类型
#### 备份时需要考虑的因素
#### 备份什么？
#### 设计备份方案
#### 备份工具
#### 备份工具的选择
#### 逻辑备份工具：
* mysqldump
* mydumper
* phpMyadmin

#### mysqldump实现逻辑备份 + binary log

#### 基于LVM2的快照实现备份 + binary log

#### xtrabackup

---

### MySQL复制
* 扩展方式
* MySQL的扩展
* 复制的功用
* 主从复制

### 演示的模型
* 主从复制
* 主主复制
* 半同步复制
* 复制过滤器

---

### 复制架构中应该注意的问题

---

### 主主复制

---

### 半同步复制

---

### 复制过滤器

---

### 基于SSL复制

---

### 跟复制功能相关的文件

---

### 复制的监控和维护

----

## MySQL读写分离

### MySQL分布式系统
### MySQL复制
### 切分
### MySQL Replication
### MHA：对MySQL主从节点做高可用
### 读写分离
### 复制的问题和解决方案
### 数据库服务衡量指标
### Galera Cluster

---
