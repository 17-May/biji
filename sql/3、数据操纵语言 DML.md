### 3.1 数据插入 INSERT
#### 3.1.1 单行插入

``` sql
-- 写法1：指定字段，按顺序赋值
INSERT INTO 表名(字段1, 字段2, 字段3)
VALUES(值1, 值2, 值3);

-- 写法2：省略字段，必须给所有字段依次赋值
INSERT INTO 表名
VALUES(值1, 值2, 值3, ...);
```

#### 3.1.2 多行批量插入

``` sql
-- 一次插入多条数据
INSERT INTO 表名(字段1, 字段2)
VALUES
(值1, 值2),
(值3, 值4),
(值5, 值6);
```

#### 3.1.3 查询结果插入（将查询数据导入表）

``` sql
-- 语法：把查询结果插入目标表
INSERT INTO 目标表名(字段1, 字段2)
SELECT 字段1, 字段2 FROM 源表名 [WHERE 条件];

-- 整表数据复制插入
INSERT INTO 目标表名
SELECT * FROM 源表名;
```

---

### 3.2 数据更新 UPDATE

> 注意：不加 `WHERE` 会更新全表数据，生产环境慎用

#### 3.2.1 单表更新

``` sql
-- 基础单表更新
UPDATE 表名
SET 字段1 = 新值1, 字段2 = 新值2
[WHERE 筛选条件];

-- 示例
UPDATE student SET age = 20 WHERE id = 1;
```

#### 3.2.2 多表联合更新

``` sql
-- 内连接多表更新
UPDATE 表1
JOIN 表2 ON 表1.关联字段 = 表2.关联字段
SET 表1.字段 = 新值, 表2.字段 = 新值
[WHERE 条件];

-- 逗号写法（旧语法）
UPDATE 表1, 表2
SET 表1.字段 = 新值
WHERE 表1.关联字段 = 表2.关联字段 AND 筛选条件;
```

---

### 3.3 数据删除 DELETE / TRUNCATE

#### 3.3.1 普通删除（单表按条件删除）

``` sql
-- 按条件删除指定数据
DELETE FROM 表名 WHERE 筛选条件;

-- 无 WHERE：删除表中所有数据（保留表结构、自增计数）
DELETE FROM 表名;
```

#### 3.3.2 清空表 TRUNCATE

``` sql
-- 清空全表数据，重置自增，效率高于 DELETE
TRUNCATE TABLE 表名;
```

#### 3.3.3 多表联合删除

``` sql
-- 方式1：JOIN 关联删除多张表数据
DELETE 表1, 表2
FROM 表1
JOIN 表2 ON 表1.关联字段 = 表2.关联字段
[WHERE 筛选条件];

-- 方式2：逗号关联写法
DELETE 表1, 表2
FROM 表1, 表2
WHERE 表1.关联字段 = 表2.关联字段 AND 筛选条件;
```