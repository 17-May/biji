### 5.1 用户管理

#### 5.1.1 创建用户

``` sql
-- 基础创建用户，指定访问主机与密码
CREATE USER '用户名'@'主机地址' IDENTIFIED BY '密码';

-- 允许任意主机访问
CREATE USER '用户名'@'%' IDENTIFIED BY '密码';

-- 仅本地访问
CREATE USER '用户名'@'localhost' IDENTIFIED BY '密码';
```

#### 5.1.2 修改用户（主机、用户名）

``` sql
-- 修改访问主机
RENAME USER '原用户名'@'原主机' TO '新用户名'@'新主机';
```

#### 5.1.3 删除用户

``` sql
-- 删除指定用户
DROP USER '用户名'@'主机地址';

-- 存在则删除，避免报错
DROP USER IF EXISTS '用户名'@'主机地址';
```

### 5.2 权限授予 GRANT

``` sql
-- 授予单库单表指定权限
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机地址';

-- 授予单个数据库所有权限
GRANT ALL PRIVILEGES ON 数据库名.* TO '用户名'@'主机地址';

-- 授予所有数据库、所有表全部权限
GRANT ALL PRIVILEGES ON *.* TO '用户名'@'主机地址';

-- 授权并允许该用户转发权限（WITH GRANT OPTION）
GRANT 权限列表 ON *.* TO '用户名'@'主机地址' WITH GRANT OPTION;

-- 常用权限示例：查询、插入、更新、删除
GRANT SELECT,INSERT,UPDATE,DELETE ON test.* TO 'user'@'%';
```

### 5.3 权限撤销 REVOKE

``` sql
-- 撤销指定权限
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机地址';

-- 撤销全部权限
REVOKE ALL PRIVILEGES ON *.* FROM '用户名'@'主机地址';

-- 撤销转发权限
REVOKE GRANT OPTION ON *.* FROM '用户名'@'主机地址';
```

### 5.4 密码修改、账户锁定、权限查看

#### 5.4.1 修改用户密码

``` sql
-- 方式1：SET 修改密码（My sql 5.7/8.0通用）
SET PASSWORD FOR '用户名'@'主机地址' = '新密码';

-- 方式2：ALTER USER 修改密码（推荐，高版本优先）
ALTER USER '用户名'@'主机地址' IDENTIFIED BY '新密码';
```

#### 5.4.2 账户锁定与解锁

``` sql
-- 锁定账户
ALTER USER '用户名'@'主机地址' ACCOUNT LOCK;

-- 解锁账户
ALTER USER '用户名'@'主机地址' ACCOUNT UNLOCK;
```

#### 5.4.3 查看权限

``` sql
-- 查看当前用户权限
SHOW GRANTS;

-- 查看指定用户权限
SHOW GRANTS FOR '用户名'@'主机地址';

-- 查看所有用户信息
SELECT user,host FROM my sql.user;
```

### 5.5 角色管理（My sql 8.0+ 支持）

#### 5.5.1 创建角色

``` sql
CREATE ROLE 角色名;
CREATE ROLE 角色名1,角色名2;
```

#### 5.5.2 给角色授权

``` sql
GRANT 权限列表 ON 数据库.表 TO 角色名;
```

#### 5.5.3 将角色分配给用户

``` sql
GRANT 角色名 TO '用户名'@'主机地址';
```

#### 5.5.4 启用角色

``` sql
-- 会话级启用角色
SET DEFAULT ROLE 角色名 TO '用户名'@'主机地址';
SET ROLE 角色名;
```

#### 5.5.5 撤销角色权限 / 回收角色

``` sql
-- 撤销角色的权限
REVOKE 权限列表 ON 数据库.表 FROM 角色名;

-- 从用户回收角色
REVOKE 角色名 FROM '用户名'@'主机地址';
```

#### 5.5.6 删除角色

``` sql
DROP ROLE 角色名;
DROP ROLE IF EXISTS 角色名;
```

### 补充：刷新权限

``` sql
-- 权限操作后刷新生效
FLUSH PRIVILEGES;
```