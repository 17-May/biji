##### 获取初始密码
```
mysqld --initialize --console
```
##### 设置环境变量(path)
```
D:\devTools\mysql-8.0.45-winx64\bin
```
##### 安装服务
```
mysqld install [服务名](一般是mysql,或者mysql+大版本号)
```
##### 启动服务
```
net start [服务名]
```
##### 进入myql
```
mysql -u root -p 
```
##### 修改密码
```
ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';
```