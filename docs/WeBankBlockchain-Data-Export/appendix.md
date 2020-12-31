## 附录


### Java安装

#### Ubuntu环境安装Java

```
# 安装默认Java版本(Java 8或以上)
sudo apt install -y default-jdk
# 查询Java版本
java -version 
```

#### CentOS环境安装Java

```
# 查询centos原有的Java版本
$ rpm -qa|grep java
# 删除查询到的Java版本
$ rpm -e --nodeps java版本
# 查询Java版本，没有出现版本号则删除完毕
$ java -version
# 创建新的文件夹，安装Java 8或以上的版本，将下载的jdk放在software目录
# 从openJDK官网(https://jdk.java.net/java-se-ri/8)或Oracle官网(https://www.oracle.com/technetwork/java/javase/downloads/index.html)选择Java 8或以上的版本下载，例如下载jdk-8u201-linux-x64.tar.gz
$ mkdir /software
# 解压jdk 
$ tar -zxvf jdk-8u201-linux-x64.tar.gz
# 配置Java环境，编辑/etc/profile文件 
$ vim /etc/profile 
# 打开以后将下面三句输入到文件里面并退出
export JAVA_HOME=/software/jdk-8u201-linux-x64.tar.gz
export PATH=$JAVA_HOME/bin:$PATH 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# 生效profile
$ source /etc/profile 
# 查询Java版本，出现的版本是自己下载的版本，则安装成功。
java -version 
```

### Git安装

git：用于拉取最新代码

**centos**:
```
sudo yum -y install git
```

**ubuntu**:
```
sudo apt install git
```

### Mysql安装

此处以Centos安装MariaDB为例。MariaDB数据库是 MySQL 的一个分支，主要由开源社区在维护，采用 GPL 授权许可。MariaDB完全兼容 MySQL，包括API和命令行。其他安装方式请参考[MySQL官网](https://dev.mysql.com/downloads/mysql/)。

（1）安装MariaDB

- 安装命令

```shell
sudo yum install -y mariadb*
```

（2）启停

```shell
启动：sudo systemctl start mariadb.service
停止：sudo systemctl stop  mariadb.service
```

（3）设置开机启动

```
sudo systemctl enable mariadb.service
```

（4）初始化root用户

```shell
执行以下命令：
sudo mysql_secure_installation
以下根据提示输入：
Enter current password for root (enter for none):<–初次运行直接回车
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
Disallow root login remotely? [Y/n] <–是否禁止root远程登录，回车
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
```

- 使用root用户登录，密码为初始化设置的密码

```
mysql -uroot -p -h localhost -P 3306
```

- 授权root用户远程访问

  **注意，以下语句仅适用于开发环境，不能直接在实际生产中使用！！！ 以下操作仅供参考，请勿直接拷贝，请自定义设置复杂密码。**

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

  **安全温馨提示：**

- 例子中给出的数据库密码（123456）仅为样例，强烈建议设置成复杂密码
- 例子中root用户的远程授权设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号

（5）创建test用户并授权本地访问

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'test'@localhost IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

（6）测试是否成功

- 登录数据库

```shell
mysql -utest -p123456 -h localhost -P 3306
```

- 创建数据库

```sql
mysql > create database databee;
mysql > use databee;

```

  **以上语句仅适用于开发环境，不能直接在实际生产中使用！！！以上设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号**
  
  
  
### Elasticsearch 安装

[ES部署](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/index.html)
  
### zookeeper 安装
zookeeper 支持单机和集群部署，推荐使用集群部署的方式，请参考zookeeper官网的说明：

[集群部署](https://zookeeper.apache.org/doc/r3.4.13/zookeeperAdmin.html#sc_zkMulitServerSetup)

[单机部署](https://zookeeper.apache.org/doc/r3.4.13/zookeeperAdmin.html#sc_singleAndDevSetup)

### supervisor安装与部署

##### 安装脚本
> sudo yum -y install supervisor

会生成默认配置/etc/supervisord.conf和目录/etc/supervisord.d，如果没有则自行创建。

##### 配置脚本
cd /etc/supervisord.d
修改/etc/supervisord.conf的[include]部分：

```shell
[include]
files = supervisord.d/*.ini
[supervisord]
```

在/etc/supervisord.d目录下配置以下启动配置文件databee_config1.ini（请注意配置文件里需要包含databee，否则会导致关闭任务命令失效），注意修改相关的路径。
```shell
[program:supervisor_databee]
directory =【你的程序路径】/WeBankBlockchain-Data-Export-core/dist ; 程序的启动目录
command = nohup java -jar 【你的安装包名，如WeBankBlockchain-Data-Export-core0.3.0-SNAPSHOT.jar】 & ; 启动命令，与命令行启动的命令是一样的
autostart = true     ; 在 supervisord 启动的时候也自动启动
startsecs = 15        ; 启动 15 秒后没有异常退出，就当作已经正常启动了
autorestart = true   ; 程序异常退出后自动重启
startretries = 3     ; 启动失败自动重试次数，默认是 3
user = app          ; 用哪个用户启动
redirect_stderr = true  ; 把 stderr 重定向到 stdout，默认 false
stdout_logfile_maxbytes = 150MB  ; stdout 日志文件大小，默认 50MB
stdout_logfile_backups = 20     ; stdout 日志文件备份数
stderr_logfile=【你的日志路径】/WeBankBlockchain-Data-Export-core/dist/log/data_bee_error.log
stdout_logfile = 【你的日志路径】/WeBankBlockchain-Data-Export-core/dist/log/data_bee_out.log  ;日志统一放在log目录下
[supervisord]
```

##### 启动任务
supervisor支持supervisorctl和supervisord启动，可通过systemctl实现开机自启动。
我们建议采用supervisord的方式启动：

```shell
supervisord -c /etc/supervisord.d/databee_config1.ini
```

##### 关闭任务
```shell
ps -ef|grep supervisord|grep databee| awk '{print $2}'|xargs kill -9
ps -ef|grep WeBankBlockchain-Data-Export|grep -v grep| awk '{print $2}'|xargs kill -9
```







