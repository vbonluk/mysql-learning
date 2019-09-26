# mysql-learning

This repo just for learning sql.

### 一.基本查询

	SELECT * FROM 表

### 二.条件查询

#### 1. 单个条件

	SELECT * FROM 表 WHERE id = 1;
   
#### 2. 多个条件 AND
	
	SELECT * FROM 表 WHERE id = 1 AND age = 18;

#### 3. 多个条件 OR
	
	SELECT * FROM 表 WHERE id = 1 OR age = 18;
	
#### 4. 多个条件 NOT <>

	SELECT * FROM 表 WHERE NOT id = 1;
	
	SELECT * FROM 表 WHERE id <> 1;
	
#### 5. 投影查询

1. 让结果集仅包含指定列
	
		SELECT id,age,name FROM 表
	
2. 给每一列起个别名
	
		SELECT id 别名1,age 别名2,name 别名3 FROM 表
		
#### 6. 排序 ORDER BY

	SELECT id,name FROM 表 ORDER BY age;

	DESC(倒序,大到小),ASC(正序,小到大,默认)

	SELECT id,name FROM 表 ORDER BY age DESC;
	
#### 7. 分页查询,数据量太大，分页查询，每次显示3条

1. 每页3条数据，获取第1页数据。

		SELECT id,name FROM 表 ORDER BY age DESC LIMIT 3 OFFSET 0;

2. 获取第3页的数据

		SELECT id,name FROM 表 ORDER BY age DESC LIMIT 3 OFFSET 6;

pageIndex = 0
	
LIMIT  --> pageSize
	
OFFSET --> pageSize * (pageIndex - 1)
	
LIMIT 3 OFFSET 0 可简写成: LIMIT 0,3

#### 8. 聚合查询
	
1. 计算总行数
	
		SELECT COUNT(*) FROM 表
	
2. 对结果取个别名

		SELECT COUNT(*) 别名 FROM 表

|  函数类型  | 含义  |
|  ----  | ----  |
|COUNT      | 计算总行数|
|SUM        | 计算某一列的合计值|
|AVG        | 计算某一列的平均值|
|MAX        | 计算某一列的最大值(不限于数值类型)|
|MIN        | 计算某一列的最小值(不限于数值类型)|

3. 分组 GROUP BY , 将结果集按照某些条件分成几个组(例如班级,多行结果,不是多列)
	
	* 查询每个班级有多少学生
		
			SELECT COUNT(*) 学生总数量 FROM 表 GROUP BY class_id;
	
	* 查询每个班级有多少学生,并显示班级id
		
			SELECT class_id,COUNT(*) 学生总数量 FROM 表 GROUP BY class_id;
	
	* 多列,例如统计每个班级有多少男生和女生
		
			SELECT class_id,COUNT(*) 学生总数量 FROM 表 GROUP BY class_id,sex;

#### 9. 多表查询

1. 笛卡尔查询,返回所有表的内容，数量是乘积的。对两个都有100条数量的表进行查询将返回1万条数据

		SELECT * FROM 表1,表2
	
2. 给两张表内含有的相同的字段设置别名，好区分(例如id)
	
		SELECT 表1.id 表1的id别名,表2.id 表2的id别名,name,age FROM 表1,表2
	
	还可以给表设置别名
	
		SELECT b1.id 表1的id别名,b2.id 表2的id别名,name,age FROM 表1 b1,表2 b2

	还可以使用WHERE语句进行进一步的条件查询

		SELECT b1.id 表1的id别名,b2.id 表2的id别名,name,age FROM 表1 b1,表2 b2 WHERE b1.sex = 1 AND b2.id = 1

#### 10. 连接查询(属于多表查询),对多个表进行JOIN运算,简单地说就是先确定一个主表，把其他表的行有选择性地“连接”到主表结果集上

1. 内连接查询(常用):INNER JOIN(可以简写成:JOIN)

	b1表中只有class_id没有class_name,b2中有class_name,将b2的班级名连接到b1的结果集里面：b2.name 班级名别名
	
		SELECT b1.id,b1.name,b1.class_id,b2.name 班级名的别名 FROM 表1 b1 INNER JOIN 表2 b2 ON b1.class_id = b2.id
		
2. 外连接:LEFT OUTER JOIN (LEFT JOIN), RIGHT OUTER JOIN (RIGHT JOIN), FULL OUTER JOIN(FULL JOIN,mysql不支持需要使用union)

	* 区别：
		
		- 内连接:
		
			* INNER JOIN
				
				仅包含两张表内共有的数据(一行数据中某列为NULL的不计入)
				
					SELECT A.id,A.name,B.class_name FROM A JOIN B ON A.class_id = B.id
		- 外连接:
		
			* LEFT OUTER JOIN
		
				包含左边表(A)所有的数据(含有NULL值的都计入)
		
					SELECT A.id,A.name,B.class_name FROM A LEFT JOIN B ON A.class_id = B.id
		
			* RIGHT OUTER JOIN
		
				包含右边表(B)所有的数据(含有NULL值的都计入)
		
					SELECT A.id,A.name,B.class_name FROM A RIGHT JOIN B ON A.class_id = B.id
		
			* FULL OUTER JOIN
		
				包含两张表所有的数据
		
					SELECT A.id,A.name,B.class_name FROM A FULL JOIN B ON A.class_id = B.id

3. 三表查询:

	* 这里使用内连接做例子(多行sql显示,实则为一条sql语句)
	
			SELECT d.userId, d.userPhoNum, a.orderId, a.productType, b.courseId, b.courseName, c.payJe
			FROM bskgk.order_info_detail a 
			JOIN bskgk.course_info b ON a.productId = b.courseId 
			JOIN bskgk.order_info c ON c.orderId = a.orderId 
			JOIN bskgk.user_info d ON d.userId = c.userId
			WHERE a.payJe > 0;     ＃as可以省略

4. 多表连接查询:
	
	参考三表查询,其实就是回归连接查询的定义,即往主表的查询结果集填充数据,有多少个需要其他表提供的数据的就查询多少张表，就JOIN多少次，关系靠ON后面的条件来关联。
	
### 三.修改数据(增删改)
1. 增(INSERT),返回0,或1
	
		INSERT INTO 表 (name,class_id) VALUES('test',1)

2. 删(DELETE),返回删除行数可能大于1

		DELETE FROM 表 WHERE id=1

3. 改(UPDATE),返回更新行数可能大于1

	* 更新单条数据

			UPDATE 表 SET name='test2',class_id=2 WHERE id = 1

	* 更新多条数据

			UPDATE 表 SET name='test3',class_id=3 WHERE id>1 AND id < 10

	* 更新字段时使用表达式运算

			UPDATE 表 SET name='test4',point=point+10 WHERE id=1

	* 没有WHERE的情况,特别危险的操作,等于将全部数据更新了.

			UPDATE 表 SET name='test2',class_id=2

### 四.事务(把多条语句作为一个整体进行操作的功能，被称为数据库事务)
对于单条SQL语句，数据库系统自动将其作为一个事务执行，这种事务被称为隐式事务。

要手动把多条SQL语句作为一个事务执行，使用 BEGIN 开启一个事务，使用 COMMIT 提交一个事务，这种事务被称为显式事务

例:

	BEGIN;
	UPDATE accounts SET balance = balance - 100 WHERE id = 1;
	UPDATE accounts SET balance = balance + 100 WHERE id = 2;
	COMMIT;

手动使事务失败,使用 ROLLBACK 回滚事务

例:

	BEGIN;
	UPDATE accounts SET balance = balance - 100 WHERE id = 1;
	UPDATE accounts SET balance = balance + 100 WHERE id = 2;
	ROLLBACK;

1. 隔离级别

	用于避免多个事务同时并发操作同个数据时，产生的数据错误，导致:脏读(脏数据),不可重复读,幻读等

	* Read Uncommitted 级别(最低级别)
	
		假定有A,B两个事务。A事务在执行过程中，B事务对同一个数据进行读取，那么就算A事务还没提交(COMMIT,或者ROLLBACK),B事务也可以读取得到，这就回导致B事务读取到的有可能是脏数据。
	
	* Read Committed 级别(比Read Uncommitted高一个级别)
	
		假定有A,B两个事务。

### 五.实用SQL语句

### 六.Mysql的管理

安装(centos 7) mysql 5.7

	下载mysql源安装包
	wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
	
	安装mysql源
	yum localinstall mysql57-community-release-el7-8.noarch.rpm
	
	检查mysql源是否安装成功
	yum repolist enabled | grep "mysql.*-community.*"
	
	安装MySQL
	yum install mysql-community-server
	
	启动MySQL服务
	systemctl start mysqld
	
	查看MySQL的启动状态
	systemctl status mysqld
	
	设置开机启动
	systemctl enable mysqld
	systemctl daemon-reload
	
	初始化（设置root密码）
	Mysql 5.7(含)以前
	
	查看配置文件
	cat /etc/my.cnf
	找到打印日志路径
	log-error=/var/log/mysqld.log
	这一项，可以知道日志路径
	然后使用grep命令查询密码
	grep 'temporary password' /var/log/mysqld.log
	得到多个结果的话，一般可能安装过多次mysql，使用最后一条即可。
	
	然后使用root登录mysql
	mysql -u root -p
	
	修改密码，执行以下sql语句
	ALTER USER 'root'@'localhost' IDENTIFIED BY '新的密码，大小写特殊符号都要有';
	
	默认root关闭远程访问(安全)，一般要新建远程的mysql用户来管理
	
	Mysql 5.7以后
	可用使用
	mysqld --initialize
	mysqld --initialize-insecure (不生成临时密码)
	来初始化。
	
数据库编码

	vim /etc/my.cnf
	
	修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：
	
	character_set_server=utf8
	init_connect='SET NAMES utf8'
	
	如果需要修改端口，再加这一段
	port=1388
	
	重新启动mysql服务使配置生效：
	systemctl restart mysqld
	
新建/删除用户,以及权限管理
	
	登录root帐号后
	查看所有用户
	use mysql;//切换到mysql库
	select host,user from user;//查询所有用户
	
	在mysql库创建用户
	create user 用户名 identified by '密码，大小写特殊字符数字';
	
	删除用户
	drop user 用户名;
	
	查看用户的权限
	
	show grants for 用户名;
	
	设置权限
	grant select on 数据库名.* to 用户名;
	
	收回权限
	revoke select on 数据库名.* from 用户名;
	
	删除某个用户的某个数据库所有权限
	revoke all on 数据库名.* from 用户名;
	
	刷新权限（使设置的权限生效）
	flush privileges;
	
	权限列表：
	ALL PRIVILEGES - 正如我们前面所看到的，这将允许MySQL用户访问指定的数据库（或者如果系统中没有选择数据库）
	CREATE-允许他们创建新的表或数据库
	DROP-允许他们删除表或数据库
	DELETE-允许他们从表中删除行
	INSERT-允许它们向表中插入行
	SELECT-允许他们使用Select命令来读取数据库
	UPDATE-允许他们更新表行
	GRANT OPTION - 允许他们授予或删除其他用户的权限
	
登录mysql

	本地登录
	mysql -u root -p
	远程登录
	mysql -h 你的ip -P 端口 -u root -p
	

查看所有数据库

	show databases;
	
查看数据库编码

	show variables like '%char%';

创建数据库

	create database 数据库名;

查看某个创建数据库的SQL语句

	show create database 数据库名;
	
切换库

	use 数据库名;
	
查看当前所在的库

	select database();
	
删除库

	drop database 数据库名;
	
删除表

	use 数据库名;//切换到该表所在的数据库
	drop table 表名;
	
遇到删表，删库等操作mysql无响应的时候，一直卡住，重新登录mysql，或者取消当前任务(ctrl + c)，然后查看数据库当前进程

	show full processlist;
	
使用 kill + 进程id 语句来杀进程

	kill 2;
	
数据库备份/还原

	逻辑备份：
	
	利用mysql自带的mysqldump

	备份所有数据库
	登录mysql后执行
	mysqldump --all-databases > 文件名.sql
	
	备份指定的数据库
	mysqldump --databases db1 db2 db3 > 文件名.sql
	
	如果需要压缩文件，在文件名.sql后加.gz
	
	当我们只备份一个数据的时候可以省去 --databases 直接写成：mysqldump test > dump.sql 不过有一些细微的差别，如果不加的话，数据库转储输出不包含创建数据库和use语句，所以可以不加这个参数直接导入到其它名字的数据库里。
	
	物理备份：
	停机维护，利用命令（如cp、tar、scp等）将数据库的存储文件复制到其他磁盘或者目录。防止数据不一致，需要停机维护的时候才备份。
	
	全量备份，增量备份：
	待续
	
	数据库恢复：
	cd 到备份文件sql目录下
	source 文件名.sql
	
退出数据库

	exit
	quit
	

防火墙开放端口设置(iptable)
	
	参考：https://blog.csdn.net/qq_43308140/article/details/90443818
	
	查看防火墙配置
	iptables -L -n
	
	编辑防火墙配置(开放mysql端口)
	vi /etc/sysconfig/iptables	
	
	增加规则
	-A INPUT -m state --state NEW -m tcp -p tcp --dport 1388 -j ACCEPT
	
	:wq保存退出vim的编辑模式后
	重启防火墙
	systemctl restart iptables.service
	
