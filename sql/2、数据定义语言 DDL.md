## 2.1 数据库操作

### 2.1.1 创建数据库

``` sql
-- 基础创建
CREATE DATABASE 数据库名;

-- 指定字符集与排序规则，不存在才创建（推荐）
CREATE DATABASE IF NOT EXISTS 数据库名
DEFAULT CHARACTER SET 字符集名
DEFAULT COLLATE 排序规则名;
```

### 2.1.2 查看数据库

``` sql
-- 查看所有数据库
SHOW DATABASES;

-- 查看数据库创建语句、字符集等信息
SHOW CREATE DATABASE 数据库名;

-- 查看当前使用的数据库
SELECT DATABASE();
```

### 2.1.3 修改数据库

``` sql
-- 修改数据库字符集
ALTER DATABASE 数据库名
DEFAULT CHARACTER SET 新字符集;

-- 修改排序规则
ALTER DATABASE 数据库名
DEFAULT COLLATE 新排序规则;
```

### 2.1.4 删除数据库

``` sql
-- 直接删除
DROP DATABASE 数据库名;

-- 存在才删除，避免报错
DROP DATABASE IF EXISTS 数据库名;
```

### 2.1.5 切换数据库

``` sql
USE 数据库名;
```
## 2.2 数据表操作

### 2.2.1 创建表

``` sql
-- 基础建表语法
CREATE TABLE IF NOT EXISTS 表名 (
    字段名1 数据类型 [约束],
    字段名2 数据类型 [约束],
    ...
) [表选项(字符集/排序规则/存储引擎)];

-- 示例
CREATE TABLE IF NOT EXISTS student (
    id INT,
    name VARCHAR(20),
    age INT
) ENGINE = InnoDB DEFAULT CHARSET = utf8mb4;
```

### 2.2.2 查看表

``` sql
-- 查看当前库所有表
SHOW TABLES;

-- 查看表结构
DESC 表名;
DESCRIBE 表名;
SHOW COLUMNS FROM 表名;

-- 查看表创建语句、引擎、字符集等详情
SHOW CREATE TABLE 表名;
```

### 2.2.3 修改表结构

``` sql
-- 添加字段
ALTER TABLE 表名 ADD [COLUMN] 字段名 数据类型 [约束];

-- 在指定字段后添加字段
ALTER TABLE 表名 ADD [COLUMN] 字段名 数据类型 AFTER 已有字段名;

-- 在表首位添加字段
ALTER TABLE 表名 ADD [COLUMN] 字段名 数据类型 FIRST;

-- 修改字段数据类型、约束
ALTER TABLE 表名 MODIFY [COLUMN] 字段名 新数据类型 [新约束];

-- 重命名字段
ALTER TABLE 表名 RENAME COLUMN 原字段名 TO 新字段名;

-- 删除字段
ALTER TABLE 表名 DROP [COLUMN] 字段名;

-- 修改表的存储引擎/字符集
ALTER TABLE 表名 ENGINE = 新引擎;
ALTER TABLE 表名 DEFAULT CHARSET = 新字符集;
```

### 2.2.4 重命名表

``` sql
-- 方式1
ALTER TABLE 原表名 RENAME TO 新表名;

-- 方式2
RENAME TABLE 原表名 TO 新表名;
```

### 2.2.5 删除表

``` sql
-- 直接删除单表
DROP TABLE 表名;

-- 存在则删除，支持多张表同时删除
DROP TABLE IF EXISTS 表名1, 表名2;
```

---

## 2.3 完整性约束

### 2.3.1 非空约束 NOT NULL

``` sql
-- 建表时指定
CREATE TABLE t1 (
    id INT,
    name VARCHAR(20) NOT NULL
);

-- 后期添加非空约束
ALTER TABLE 表名 MODIFY 字段名 数据类型 NOT NULL;

-- 删除非空约束
ALTER TABLE 表名 MODIFY 字段名 数据类型 NULL;
```

### 2.3.2 默认值约束 DEFAULT

``` sql
-- 建表时指定默认值
CREATE TABLE t2 (
    id INT,
    sex VARCHAR(10) DEFAULT '未知'
);

-- 后期添加/修改默认值
ALTER TABLE 表名 ALTER COLUMN 字段名 SET DEFAULT 值;

-- 删除默认值
ALTER TABLE 表名 ALTER COLUMN 字段名 DROP DEFAULT;
```

### 2.3.3 唯一约束 UNIQUE

``` sql
-- 单列唯一（建表）
CREATE TABLE t3 (
    id INT,
    phone VARCHAR(11) UNIQUE
);

-- 联合唯一、命名约束
CREATE TABLE t3 (
    id INT,
    name VARCHAR(20),
    age INT,
    UNIQUE KEY uk_name_age (name, age)
);

-- 后期添加唯一约束
ALTER TABLE 表名 ADD UNIQUE [索引名] (字段名);

-- 删除唯一约束
ALTER TABLE 表名 DROP INDEX 唯一索引名;
```

### 2.3.4 主键约束 PRIMARY KEY

``` sql
-- 单列主键
CREATE TABLE t4 (
    id INT PRIMARY KEY,
    name VARCHAR(20)
);

-- 联合主键
CREATE TABLE t4 (
    id INT,
    num INT,
    PRIMARY KEY (id, num)
);

-- 后期添加主键
ALTER TABLE 表名 ADD PRIMARY KEY (字段名);

-- 删除主键
ALTER TABLE 表名 DROP PRIMARY KEY;
```

### 2.3.5 外键约束 FOREIGN KEY

``` sql
-- 建表时添加外键
CREATE TABLE 从表名 (
    字段1 数据类型,
    关联字段 数据类型,
    FOREIGN KEY (关联字段) REFERENCES 主表名(主表字段)
    [ON DELETE 动作] [ON UPDATE 动作]
);

-- 后期添加外键
ALTER TABLE 从表名
ADD CONSTRAINT 外键名 FOREIGN KEY (从表字段) REFERENCES 主表(主表字段);

-- 删除外键
ALTER TABLE 表名 DROP FOREIGN KEY 外键名;
```

### 2.3.6 检查约束 CHECK（My sql 8.0+ 完整生效）

``` sql
-- 建表时添加检查约束
CREATE TABLE t5 (
    id INT,
    age INT CHECK (age > 0 AND age < 150)
);

-- 后期添加检查约束
ALTER TABLE 表名 ADD CHECK (约束条件);

-- 删除检查约束
ALTER TABLE 表名 DROP CHECK 约束名;
```

---

## 2.4 索引操作

### 2.4.1 普通索引

``` sql
-- 建表时创建
CREATE TABLE t_idx (
    id INT,
    name VARCHAR(20),
    INDEX idx_name (name)
);

-- 单独创建
CREATE INDEX 索引名 ON 表名(字段名);

-- 删除索引
DROP INDEX 索引名 ON 表名;
ALTER TABLE 表名 DROP INDEX 索引名;
```

### 2.4.2 唯一索引

``` sql
CREATE UNIQUE INDEX 索引名 ON 表名(字段名);
```

### 2.4.3 主键索引

主键索引随主键自动创建，语法同**主键约束**，无需手动单独创建。

### 2.4.4 复合索引（联合索引）

``` sql
CREATE INDEX 索引名 ON 表名(字段1, 字段2, 字段3);
```

### 2.4.5 全文索引 FULLTEXT

``` sql
-- 仅 InnoDB/MyISAM 支持
CREATE FULLTEXT INDEX 索引名 ON 表名(字段名);
```

### 2.4.6 查看索引

``` sql
SHOW INDEX FROM 表名;
SHOW KEYS FROM 表名;
```

---

## 2.5 视图操作

### 2.5.1 创建视图

``` sql
CREATE [OR REPLACE] VIEW 视图名
AS
SELECT 查询语句;

-- 示例
CREATE VIEW v_stu AS
SELECT id, name, age FROM student;
```

### 2.5.2 查看视图

``` sql
-- 查看视图结构
DESC 视图名;

-- 查看视图创建语句
SHOW CREATE VIEW 视图名;

-- 查询视图数据（用法同普通表）
SELECT * FROM 视图名;
```

### 2.5.3 修改视图

``` sql
ALTER VIEW 视图名
AS
SELECT 新查询语句;
```

### 2.5.4 删除视图

``` sql
DROP VIEW 视图名;
DROP VIEW IF EXISTS 视图名1, 视图名2;
```

---

## 2.6 自增列、临时表、表分区

### 2.6.1 自增列 AUTO_INCREMENT

``` sql
-- 建表时设置自增（一般配合主键使用）
CREATE TABLE t_auto (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20)
);

-- 修改自增起始值
ALTER TABLE 表名 AUTO_INCREMENT = 新起始值;
```

### 2.6.2 临时表

会话级别，连接断开自动销毁

``` sql
-- 创建临时表
CREATE TEMPORARY TABLE 临时表名 (
    字段1 数据类型,
    字段2 数据类型
);

-- 删除临时表
DROP TEMPORARY TABLE 临时表名;
```

### 2.6.3 表分区

#### 1）范围分区 RANGE

``` sql
CREATE TABLE 分区表名 (
    id INT,
    create_time DATE
) ENGINE = InnoDB
PARTITION BY RANGE (TO_DAYS(create_time)) (
    PARTITION p2025 VALUES LESS THAN (TO_DAYS('2026-01-01')),
    PARTITION p2026 VALUES LESS THAN (TO_DAYS('2027-01-01'))
);
```

#### 2）列表分区 LIST

``` sql
CREATE TABLE 分区表名 (
    id INT,
    area INT
) ENGINE = InnoDB
PARTITION BY LIST (area) (
    PARTITION p1 VALUES IN (1,2,3),
    PARTITION p2 VALUES IN (4,5,6)
);
```

#### 3）哈希分区 HASH

``` sql
CREATE TABLE 分区表名 (
    id INT
) ENGINE = InnoDB
PARTITION BY HASH(id)
PARTITIONS 4;
```

#### 分区管理

``` sql
-- 新增分区
ALTER TABLE 表名 ADD PARTITION (PARTITION 分区名 VALUES ...);

-- 删除分区
ALTER TABLE 表名 DROP PARTITION 分区名;

-- 查看分区信息
SHOW CREATE TABLE 表名;
SELECT * FROM INFORMATION_SCHEMA.PARTITIONS WHERE TABLE_NAME = '表名';
```