title: MySQL 5.5.45 数据文件过大优化
date: 2017-12-28 11:52:22
desc: 
tags: [优化, 配置] 
---

相信不少人都遇到过，MySQL运行一段时间后，ibdata1的文件会增长特别大。即使把数据删掉，ibdata1也没有减少。长久以往，磁盘空间早晚会被用光。
背后是因为InnoDB表的数据被集中存储在共享表空间ibdata中。在MySQL<=5.6.5，innodb_file_per_table默认为0，InnoDB表数据没有按表划分存储。而除此之外，ibdata还存储着数据字典、双写缓冲区、undo log等。
当innodb_file_per_table为0时，ibdata会不断增大，有时会导致磁盘空间不足。通常是InnoDB表的数据导致的，undo log是次要原因。因为undo log的增加通常是在事务较为繁忙的时候，且事务中做了大量的更新操作，但是undo log占用的空间却可以被重用。InnoDB的purge线程就是负责清理不需要的undo log空间以供其他的undo log使用。

所以优化思路也就是：启用innodb_file_per_table。对于历史的数据表，需要重建才能使得配置生效。

<!-- more -->

## 修改配置
1. 配置文件一般在`/etc/my.cnf`，如果没有可以自己建一个
2. 在`mysqld`分节内添加如下配置：
```
[mysqld]
innodb_file_per_table
```

## 备份数据
`mysqldump -uroot -p -q --all-databases --single-transaction > all-databases.sql`

## 重建数据库
1. 停机
    * `/etc/init.d/mysql stop`
2. 备份，避免发生意外无法恢复
    * `cd /var/lib/`
    * `mv mysql mysql-backup`
3. 重新初始化数据库
    * `/usr/bin/mysql_install_db`
4. 修改权限，否则启动会失败
    * `chmod 755 mysql`
    * `chown mysql:mysql -R mysql`
5. 启动
    * `/etc/init.d/mysql start`

### 恢复数据
1. 导入数据
    * `mysql < all-databases.sql`
2. 恢复权限
    * `mysql -uroot`
    * `flush privileges;`

### 附录
如果想要修改mysql默认数据存储地址，可以：
1. 修改`/etc/init.d/mysql`中的`datadir=`
2. 修改`/etc/my.cnf`，添加`datadir`
    
