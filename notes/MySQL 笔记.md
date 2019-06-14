# MySQL 笔记

***



## 使用MySQL

* CLI连接数据库

  连接数据库使用mysql命令

  ```
  mysql [database] [--host  hostIP] -u <username> -p 
  database:数据库，登录后默认使用的数据库，如果省略则处于无数据库状态，登录后可以使用use database切换
  --host:如果省略，默认连接本机的mysql
  username： 用户名
  -p:后接密码，如果后面没有内容（空格都没有），那么下一行会提示你输入密码
  ```
 登录后可以查看当前服务下的所有数据库
  ```
  mysql> show databases
  ```
  切换到指定的数据库
  ```
  mysql> use database
  ```


* 创建数据库
  ```
  mysql> create database <databasename>;//创建名为databasename的数据库
  ```
  例如：
  ```
  mysql> create database microletters;
   Query OK, 1 row affected (0.06 sec)
   mysql> show databases
    -> ;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| microletters       |
| mysql              |
| performance_schema |
| sys                |
| yuebodb            |
+--------------------+
6 rows in set (0.01 sec)
//切换至新建的数据库
  mysql> use microletters
  Database changed
  //暂时没有表
  mysql> show tables;
  Empty set (0.01 sec)
  ```
  

* 为新数据库创建用户并设置权限

  新建用户

  ```
  create user '<username>'@'<host>' identified by '<password>' //创建用户
  ```

  查询已有用户

  ```
   SELECT User, Host FROM mysql.user;
  ```

  删除用户

  ```
  drop user '<username>' 
  ```

  设置权限

  ```
  grant <privileges> on <database>.<*|table> to '<username>'@'<host>' [WITH GRANT OPTION]
  privileges可以是一下选项：
  1. ALL PRIVILEGES 所有权限
  2. CREATE：创建表
  3. DROP：删除表
  4. DELETE：删除表数据
  5.INSERT：插入
  6. SELECT：查询
  7. UPDATE:更新
  WITH GRANT OPTION:如果省略，那么授权后用户权限不能授权给其它用户。
  ```

  查看用户的权限

  ```
  show grants for '<username>'@'host';
  例如：
  mysql> show grants for 'kavin2019'@'localhost';
  +---------------------------------------------------------------------+
  | Grants for kavin2019@localhost                                      |
  +---------------------------------------------------------------------+
  | GRANT USAGE ON *.* TO `kavin2019`@`localhost`                       |
  | GRANT ALL PRIVILEGES ON `microletters`.* TO `kavin2019`@`localhost` |
  +---------------------------------------------------------------------+
  2 rows in set (0.01 sec)
  ```

  取消用户的权限

  ```
  REVOKE <privileges> on <database>.<*|table> from '<username>'@'<host>'
  ```

  设置或取消用户权限后都需要更新

  ```
  flush privileges;
  ```

  在使用mysql的客户端界面程序登录时，websocket方式提示一下错误：

  ```
  Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(/usr/local/mysql/lib/plugin/caching_sha2_password.so, 2): image not found
  
  注意，这个问题是 MySQL 的问题，不是客户端问题，
  我用 Navicat Premium 和 Sequel Pro 连接本地 MySQL 都是显示一样的错误。
  ```

  解决方案：

  ```
  ALTER USER 'kavin2019'@'localhost' IDENTIFIED WITH mysql_native_password BY 'kavin2019'
  ```

  

  