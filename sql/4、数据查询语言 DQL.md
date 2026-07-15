### 4.1 基础 SELECT 单表查询

``` sql
-- 查询表中所有字段
SELECT * FROM 表名;

-- 查询指定字段
SELECT 字段1, 字段2, 字段3 FROM 表名;
```

### 4.2 字段筛选、别名、去重、行数限制

``` sql
-- 字段别名（AS 可省略）
SELECT 字段1 AS 别名1, 字段2 别名2 FROM 表名;

-- 表别名
SELECT 字段 FROM 表名 AS 表别名;

-- 去重 DISTINCT
SELECT DISTINCT 字段1, 字段2 FROM 表名;

-- 限制返回行数 LIMIT
SELECT * FROM 表名 LIMIT 行数;
```

### 4.3 条件查询 WHERE、排序 ORDER BY

``` sql
-- 条件查询
SELECT * FROM 表名 WHERE 条件表达式;

-- 单字段排序（ASC 升序默认，DESC 降序）
SELECT * FROM 表名 ORDER BY 字段名 ASC;
SELECT * FROM 表名 ORDER BY 字段名 DESC;

-- 多字段排序
SELECT * FROM 表名 ORDER BY 字段1 DESC, 字段2 ASC;
```

### 4.4 聚合函数、分组 GROUP BY、分组过滤 HAVING

``` sql
-- 常用聚合函数：COUNT、SUM、AVG、MAX、MIN
SELECT COUNT(*), SUM(字段), AVG(字段), MAX(字段), MIN(字段) FROM 表名;

-- 基础分组查询
SELECT 分组字段, 聚合函数(字段) FROM 表名 GROUP BY 分组字段;

-- 分组 + 分组前筛选 WHERE
SELECT 分组字段, 聚合函数(字段) FROM 表名 
WHERE 普通条件 
GROUP BY 分组字段;

-- 分组 + 分组后过滤 HAVING
SELECT 分组字段, 聚合函数(字段) FROM 表名 
GROUP BY 分组字段 
HAVING 聚合条件;

-- 完整组合：WHERE + GROUP BY + HAVING + ORDER BY
SELECT 分组字段, 聚合函数(字段) FROM 表名
WHERE 普通条件
GROUP BY 分组字段
HAVING 聚合条件
ORDER BY 聚合函数(字段) DESC;
```

### 4.5 多表连接

``` sql
-- 交叉连接（笛卡尔积）
SELECT * FROM 表1, 表2;

-- 内连接 INNER JOIN（只显示匹配数据）
SELECT * FROM 表1
INNER JOIN 表2 ON 表1.关联字段 = 表2.关联字段;

-- 左外连接 LEFT JOIN（左表全部数据，右表匹配则显示）
SELECT * FROM 表1
LEFT JOIN 表2 ON 表1.关联字段 = 表2.关联字段;

-- 右外连接 RIGHT JOIN（右表全部数据，左表匹配则显示）
SELECT * FROM 表1
RIGHT JOIN 表2 ON 表1.关联字段 = 表2.关联字段;

-- 全连接（My sql 无 FULL JOIN，用 UNION 模拟）
SELECT * FROM 表1 LEFT JOIN 表2 ON 表1.关联字段=表2.关联字段
UNION
SELECT * FROM 表1 RIGHT JOIN 表2 ON 表1.关联字段=表2.关联字段;
```

### 4.6 子查询

``` sql
-- 标量子查询（结果单行单列）
SELECT * FROM 表名 WHERE 字段 = (SELECT 字段 FROM 表名 WHERE 条件);

-- 列子查询（结果多行单列，搭配 IN / ANY / ALL）
SELECT * FROM 表名 WHERE 字段 IN (SELECT 字段 FROM 表名 WHERE 条件);

-- 行子查询（结果单行多列）
SELECT * FROM 表名 WHERE (字段1, 字段2) = (SELECT 字段1, 字段2 FROM 表名 WHERE 条件);

-- 表子查询（结果多行多列，当作临时表）
SELECT * FROM (SELECT * FROM 表名 WHERE 条件) 临时表别名;

-- 相关子查询（内外查询相互关联）
SELECT * FROM 表1 t1 
WHERE EXISTS (SELECT * FROM 表2 t2 WHERE t1.关联字段 = t2.关联字段);
```

### 4.7 联合查询 UNION / UNION ALL

``` sql
-- UNION：合并结果集，自动去重
SELECT 字段1, 字段2 FROM 表1
UNION
SELECT 字段1, 字段2 FROM 表2;

-- UNION ALL：合并结果集，不去重、效率更高
SELECT 字段1, 字段2 FROM 表1
UNION ALL
SELECT 字段1, 字段2 FROM 表2;
```

### 4.8 分页查询、衍生表查询

``` sql
-- 分页查询 LIMIT 起始索引, 每页条数（索引从 0 开始）
SELECT * FROM 表名 LIMIT 起始下标, 每页记录数;

-- 衍生表查询（子查询作为衍生表，必须起别名）
SELECT 字段 FROM (SELECT 字段1, 字段2 FROM 表名 WHERE 条件) 衍生表别名
WHERE 外部条件;
```