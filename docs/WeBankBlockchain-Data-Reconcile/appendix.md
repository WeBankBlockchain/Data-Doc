# 附录


## 1. MySql的安装

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

```eval_rst
.. important::
  **安全温馨提示：**

- 例子中给出的数据库密码（123456）仅为样例，强烈建议设置成复杂密码
- 例子中root用户的远程授权设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号
```

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
mysql > create database webasebee;
mysql > use webasebee;

```

  **以上语句仅适用于开发环境，不能直接在实际生产中使用！！！以上设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号**

## 2. Java安装

### 2.1. Ubuntu环境安装Java

```
# 安装默认Java版本(Java 8或以上)
sudo apt install -y default-jdk
# 查询Java版本
java -version 
```

### 2.2. CentOS环境安装Java

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

## 3. Git安装

git：用于拉取最新代码

**centos**:
```
sudo yum -y install git
```

**ubuntu**:
```
sudo apt install git
```


## 4. FTP安装

ftp：用于文件托管

**centos**:

4.1 搭建vsftp服务器
```
# 安装vsftpd
yum -y install vsftpd
# 配置vsftp
vim /etc/vsftpd/vsftpd.conf
修改其中： anonymous_enable=NO  禁止匿名登录
取消chroot_list_enable=YES，chroot_list_file=/etc/vsftpd/chroot_list的注释  
在最后一行新增 allow_writeable_chroot=YES
然后保存退出
```
4.2 增加访问ftp的用户

```
# 编辑账户文件，输入账户名，多个用户名以空格隔开
vim /etc/vsftpd/chroot_list
# 设置上传目录
mkdir -p /home/upload
# 新增用户，配置主文件夹
useradd -d /home/upload -s /sbin/nologin ftptest
# 将用户放置ftp组
usermod -aG ftp ftptest
# 将文件夹分配给用户
chown ftptest /home/upload
# 设置密码
passwd ftptest
```

4.3 修改firewall使之允许ftp功能
```
systemctl start firewalld.service
firewall-cmd --permanent --zone=public --add-service=ftp
firewall-cmd --reload
```

4.4 修改firewall使之允许ftp功能
```
# 启动ftp
systemctl start vsftpd
# 查看ftp状态
systemctl status vsftpd
# 设置开机自启动
chkconfig vsftpd on
```
4.5 安装问题

安装完成后可通过以下命令测试ftp是否可以正常登录
```
ftp localhost
```
如果无法登录，可在/etc/shells文件中，增加配置：/sbin/nologin
保存后重启ftp，尝试登录