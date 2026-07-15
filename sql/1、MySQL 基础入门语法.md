### 1.1 登录与退出命令

#### 1.1.1 本地 / 远程登录 MySQL

``` sql
-- 本地默认端口登录（交互式输入密码）
mysql -u用户名 -p

-- 本地登录并直接携带密码（不推荐，有安全风险）
mysql -u用户名 -p密码

-- 指定主机、端口登录远程 MySQL
mysql -h主机地址 -P端口号 -u用户名 -p

-- 登录并指定默认数据库
mysql -h主机地址 -P端口号 -u用户名 -p 数据库名
```

#### 1.1.2 退出 MySQL 客户端

``` sql
exit;
quit;
\q
```

### 1.2. 客户端基础操作指令

``` sql
-- 查看当前登录用户
select user();

-- 查看当前所在数据库
select database();

-- 切换/进入指定数据库
use 数据库名;

-- 查看所有数据库
show databases;

-- 查看当前库下所有数据表
show tables;

-- 查看表结构（字段、类型、约束）
desc 表名;
describe 表名;
show columns from 表名;

-- 查看表创建语句
show create table 表名;

-- 查看数据库创建语句
show create database 数据库名;

-- 清屏（部分客户端支持）
\c
system cls;  -- Windows
system clear; -- Linux/Mac

-- 执行外部 SQL 脚本
source 脚本文件绝对路径;

-- 查看 MySQL 版本
select version();
```

### 1.3. 注释、标识符、关键字、常量与变量

#### 1.3.1 注释语法

``` sql
-- 单行注释（MySQL 标准）
# 单行注释（兼容 Shell 风格）
/*
多行注释
可换行书写
*/
```

#### 1.3.2 标识符与关键字
- 标识符：数据库名、表名、字段名、别名
- 标识符引用（名称含空格 / 关键字 / 特殊字符时使用）

``` sql
`数据库名`
`表名`
`字段名`
```

#### 1.3.3 常量

``` sql
-- 字符串常量
'普通字符串'
"双引号字符串"

-- 数值常量
100       -- 整型
3.14      -- 浮点型

-- 日期时间常量
'2026-06-12'
'2026-06-12 10:30:00'

-- 空值常量
NULL
```

#### 1.3.4 变量

##### 1.3.4.1 用户自定义会话变量

``` sql
-- 定义并赋值
set @变量名 = 值;
set @name = '张三';
set @age = 20;

-- 使用变量
select @name, @age;
```

##### 1.3.4.2 局部变量（仅存储过程 / 函数内使用）

``` sql
declare 变量名 数据类型 [default 默认值];
declare num int default 0;
```

##### 1.3.4.3 系统变量（全局 / 会话）

``` sql
-- 查看所有系统变量
show variables;

-- 查看单个系统变量
show variables like '变量名';

-- 修改会话级系统变量
set 变量名 = 值;

-- 修改全局系统变量（需权限）
set global 变量名 = 值;
```

### 1.4. 数据类型体系

#### 1.4.1 数值类型

##### 整型

`TINYINT`、`SMALLINT`、`MEDIUMINT`、`INT/INTEGER`、`BIGINT`

``` sql
-- 建表示例
create table test_num(
    a tinyint,
    b int,
    c bigint unsigned  -- 无符号整型
);
```

##### 浮点型 & 定点型

`FLOAT`、`DOUBLE`、`DECIMAL(M,D)`

``` sql
create table test_float(
    f float(5,2),
    d double,
    dec decimal(10,2)
);
```

#### 1.4.2 字符串类型

`CHAR(n)`、`VARCHAR(n)`、`TEXT`、`MEDIUMTEXT`、`LONGTEXT`、`ENUM`、`SET`

``` sql
create table test_str(
    c char(10),
    vc varchar(50),
    t text,
    sex enum('男','女'),
    hobby set('看书','运动','音乐')
);
```

#### 1.4.3 日期时间类型

`DATE`、`TIME`、`DATETIME`、`TIMESTAMP`、`YEAR`

``` sql
create table test_date(
    d date,
    t time,
    dt datetime,
    ts timestamp,
    y year
);
```

#### 1.4.4 二进制（大对象）类型

`BIT`、`BINARY`、`VARBINARY`、`BLOB`、`MEDIUMBLOB`、`LONGBLOB`
#### 1.4.5 特殊类型

`JSON`、`GEOMETRY`（空间类型）
### 1.5. 运算符

#### 1.5.1 算术运算符

`+`、`-`、`*`、`/`、`%`（取模）

``` sql
select 10 + 5, 10 - 3, 2 * 6, 10 / 2, 7 % 2;
```

#### 1.5.2 比较运算符

`=`、`<>`/`!=`、`>`、`<`、`>=`、`<=`

``` sql
select 5 = 5, 5 <> 3, 10 > 2;
```

#### 1.5.3 逻辑运算符

`AND`/`&&`、`OR`/`||`、`NOT`/`!`

``` sql
select 1>0 and 2<5;
select 1>1 or 2<5;
select not (1>2);
```

#### 1.5.4 位运算符

`&`（按位与）、`|`（按位或）、`^`（按位异或）、`~`（按位取反）、`<<`、`>>`

``` sql
select 3 & 1, 3 | 1;
```

#### 1.5.5 赋值运算符

`=`、`:=`

``` sql
set @a := 100;
```

#### 1.5.6 区间运算符

``` sql
-- BETWEEN ... AND 区间判断
select * from 表名 where 字段 between 最小值 and 最大值;

-- IN 集合范围判断
select * from 表名 where 字段 in (值1,值2,值3);

-- NOT IN 不在集合内
select * from 表名 where 字段 not in (值1,值2);
```

#### 1.5.7 模糊匹配运算符

``` sql
-- LIKE 模糊查询
-- % 匹配任意字符，_ 匹配单个字符
select * from 表名 where 字段 like '张%';
select * from 表名 where 字段 like '_三';

-- NOT LIKE 不匹配
select * from 表名 where 字段 not like '李%';

-- REGEXP / RLIKE 正则匹配
select * from 表名 where 字段 regexp '正则表达式';
```

#### 1.5.8 空值判断运算符

``` sql
-- 判断是否为 NULL
字段 is null
字段 is not null
```