### 服务方式启动

#### 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |备注|
| --- | --- | --- |
| FISCO-BCOS | >= 2.0， 1.x版本请参考V0.5版本 |
| Bash | 需支持Bash（理论上来说支持所有ksh、zsh等其他unix shell，但未测试）|
| Java | JDK[1.8] | |
| Git | 下载的安装包使用Git | |
| MySQL | >= mysql-community-server[5.7] | |
| ElasticSearch | >= elasticsearch [7.0] | 只有在需要ES存储时安装 |
| zookeeper | >= zookeeper[3.4] | 只有在进行集群部署的时候需要安装|
| docker    | >= docker[18.0.0] | 只有需要可视化监控页面的时候才需要安装，docker的安装可参考[gitee docker安装手册](https://docker_practice.gitee.io/install/centos.html) |


#### 部署步骤

##### 获取工程

###### 代码拉取

```shell
git clone https://github.com/WeBankBlockchain/Data-Export.git
```

```eval_rst
.. note::
    - 如果因为网络问题导致长时间无法下载，请尝试：git clone https://gitee.com/WeBankBlockchain/Data-Export.git
```

得到工程代码，WeBankBlockchain-Data-Export的工程使用gradle进行构建，是一个使用gradle进行多工程构建的SpringBoot工程。

```
├── ChangeLog.md
├── LICENSE
├── README.md
├── tools
├── WeBankBlockchain-Data-Export-service
├── WeBankBlockchain-Data-Export-sdk
├── build.gradle
├── gradle
├── gradlew
├── gradlew.bat
├── libs
├── settings.gradle
└── src

```

其中各个子工程的说明如下：

WeBankBlockchain-Data-Export-service 数据导出服务

WeBankBlockchain-Data-Export-sdk 数据导出SDK

tools中包括了服务方式启动所需配置文件和启动脚本。

###### 进入安装路径

```shell
cd Data-Export/tools

```

tools目录如下：
```
├── tools
│   ├── config
│   │   ├── application.properties
│   ├── start.sh
│   └── stop.sh
```

```eval_rst
.. note::
    - **config为配置文件目录，使用channel方式连接区块链时，需将证书放至该目录。**
    - config包括了abi和bin两个文件夹，用于配置合约信息。
    - 运行生成的sql脚本data_export.sql和可视化脚本default_dashboard.json会保存在config目录下。
    - 运行日志保存在./tools/log目录下

```
##### 配置工程

###### 配置证书文件(channel方式启动)

选择channel方式连接链节点时，需配置证书。

将链SDK证书拷贝到 **./tools/config/resources目录**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./tools/config/
```


###### 配置文件设置

修改config/application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要修改的：

```
### The following types are supported:
### 1, Channel
### 2, JsonRPC
### 3, Data-Stash
### 选择上述三种中一种方式配置即可，推荐 Channel方式

# 1、Channel方式启动，需配置证书
## GROUP_ID必须与FISCO-BCOS中配置的groupId一致, 多群组以,分隔，如1,2
system.groupId=1
# 节点的IP及通讯端口、组号。 
##IP为节点运行的IP，PORT为节点运行的channel_port，默认为20200
system.nodeStr=[IP]:[PORT]
# 默认为./config路径
system.certPath=./config

# 2、RPC方式启动
#system.groupId=1
## 链密钥类型，1-国密，2-ECDSA
#system.cryptoTypeConfig=0
#system.rpcUrl=

# 3、数据仓库方式启动
#system.jdbcUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
#system.user=
#system.password=
## 链密钥类型，1-国密，2-ECDSA
#system.cryptoTypeConfig=0

### 数据库的信息，暂时只支持mysql； serverTimezone 用来设置时区
### 请确保在运行前创建对应的database，如果分库分表，则可配置多个数据源，如system.db1.dbUrl=\system.db1.user=\system.db0.password=
system.db0.dbUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db0.user=
system.db0.password=
```

#### 配置合约

将合约对应的abi和binary文件分别放置到config/abi和config/bin目录中。

两个目录中包含了一个HelloWorld合约的abi和bin文件，使用时请按需删除。


###### 可视化安装配置

在application.properties中将grafana打开时，将在config目录下生成可视化脚本，配置如下:
```
system.grafanaEnable=true
```


###### 创建数据库

连接MysQL数据库，创建对应的数据库，参考的命令行命令如下，请把中括号的值替换为实际值。

```shell
$ mysql -u[user_name] -h[IP] -p
Enter password: [password]
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 164
Server version: 5.7.28-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> create database [database];
Query OK, 1 row affected (0.04 sec)

mysql> 
```
只有创建成功database后，导出程序才能正常连接数据库。

###### 创建Elasticsearch

需要ES存储时，需先安装ES, 参考[ES部署](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/index.html)


##### 运行程序

```eval_rst
.. note::
        本工程默认使用gradle wrapper来实施构建。在必要时，可在start.sh时添加-c的编译选项，指定使用本机的gradle来进行代码编译。

            - 如果运行程序以后无法正常下载gradle wrapper，可自行安装gradle软件，可参考 `官网安装教程 <https://gradle.org/install/>`_ 。       
            - 如果本机已经安装了符合要求的gradle软件，则可以使用`./start.sh -c gradle`选项来指定编译方式，使用本机安装的gradle来实施构建。
```

###### 选择一：直接在本机运行

```
chmod +x start.sh
bash start.sh
```

```eval_rst
.. important::
    请务必按照以上命令操作，**请勿使用sudo命令来操作**，否则会导致Gradlew没有权限，导致导出数据失败。
```

###### 选择二：本机编译，复制执行包到其他服务器上运行

```
chmod +x start.sh
bash start.sh
```

请将此工程下的./WeBankBlockchain-Data-Export-service/dist 文件夹复制到其他服务器上，并执行：

```
chmod +x *.sh
bash start.sh
tail -f *.log
```

###### 选择三：本机编译，复制执行包到其他服务器，使用supervisor来启动。

使用supervisor来守护和管理进程，supervisor能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能自动重启。
它是通过fork/exec的方式把这些被管理的进程当作supervisor的子进程来启动，这样只要在supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去即可。也实现当子进程挂掉的时候，父进程可以准确获取子进程挂掉的信息的，可以选择是否自己启动和报警。
supervisor还提供了一个功能，可以为supervisord或者每个子进程，设置一个非root的user，这个user就可以管理它对应的进程。

使用supervisor来安装与部署的步骤请参阅[附录6](appendix.html#supervisor)

##### 检查运行状态及退出

###### 检查程序进程是否正常运行

```
ps -ef |grep Data-Export
```

如果看到如下信息，则代表进程执行正常：

```
app   21980 24843  0 15:23 pts/3    00:00:44 java -jar WeBankBlockchain-Data-Export-core1.3.1.jar
```

###### 检查程序是否已经正常执行

当你看到程序运行，并在最后出现以下字样时，则代表运行成功：

```
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
```

还可以通过以下命令来查看区块的同步状态：

```
tail -f data-export.log | grep "sync block"
```

当看到以下滚动的日志时，则代表区块同步状态正常，开始执行下载任务。

```
 $ tail -f data-export.log | grep "sync block"
2019-05-05 14:41:07.348  INFO 60538 --- [main] c.w.w.c.service.CommonCrawlerService     : Try to sync block number 0 to 90 of 90
2019-05-05 14:41:07.358  INFO 60538 --- [main] c.w.w.c.service.BlockTaskPoolService     : Begin to prepare sync blocks from 0 to 90
2019-05-05 14:41:17.142  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 0 of 90 sync block succeed.
2019-05-05 14:41:17.391  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 1 of 90 sync block succeed.
2019-05-05 14:41:17.618  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 2 of 90 sync block succeed.
2019-05-05 14:41:18.072  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 3 of 90 sync block succeed.
2019-05-05 14:41:18.395  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 4 of 90 sync block succeed.
2019-05-05 14:41:18.796  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 5 of 90 sync block succeed.
2019-05-05 14:41:19.008  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 6 of 90 sync block succeed.
2019-05-05 14:41:19.439  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 7 of 90 sync block succeed.
2019-05-05 14:41:20.303  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 8 of 90 sync block succeed.
2019-05-05 14:41:20.512  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 9 of 90 sync block succeed.
2019-05-05 14:41:20.738  INFO 60538 --- [main] c.w.w.crawler.service.BlockSyncService   : Block 10 of 90 sync block succeed.
……
```

###### 检查数据是否已经正常产生


**DB数据检查**

你也可以通过DB来检查，登录你之前配置的数据库，看到自动创建完表的信息，以及表内开始出现数据内容，则代表一切进展顺利。如你可以执行以下命令：

```
# 请用你的配置信息替换掉[]里的配置，并记得删除[]
mysql -u[用户名] -p[密码] -e "use [数据库名]; select count(*) from block_detail_info"
```

如果查询结果非空，出现类似的如下记录，则代表导出数据已经开始运行：

```
+----------+
| count(*) |
+----------+
|      633 |
+----------+
```

**ES数据检查**

可以通过url查询索引建立情况，http://ip:9200/_cat/indices?v，结果类似如下：

```
health status index                            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   helloworldsetnamemethod          hvYse4rKTJuSskQPh9ac7Q   1   1          0            0       208b           208b
yellow open   blockrawdata                     ZV6vNxfRSyGDnm_R0aR-tg   1   1         65            7      504kb          504kb
yellow open   blockdetailinfo                  Vbv9dtdCTrK1U5p9okeCfA   1   1         65            7     33.6kb         33.6kb
yellow open   blocktxdetailinfo                1seHyG6CQk6x8AKeqPsLqQ   1   1         35            0     43.3kb         43.3kb
yellow open   blockrawdatabo                   hNl3wUSsQoG2h2AHdgV-NQ   1   1          0            0       208b           208b
yellow open   txreceiptrawdata                 v-bMu_khQ8OI2TyDEhakkA   1   1         35            0    155.3kb        155.3kb
yellow open   contractinfo                     DolSTxR9ToSMLzJ3OJU31w   1   1         27            0    162.4kb        162.4kb
yellow open   deployaccountinfo                ET0VMMahRyqAuSHNLTVEhg   1   1         21            0     15.9kb         15.9kb
```

更多查询，参考[ES数据查询](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/search-your-data.html)

**停止导入程序**

```
bash stop.sh
```

恭喜您，到以上步骤，您已经完成了数据导出组件的安装和部署。如果您还需要额外获得可视化的监控页面，请参考3.3章节。


##### 多群组数据导出

首先，请配置FISCO BCOS的多群组，详情可参考[FISCO BCOS多群组部署](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/group_use_cases.html?highlight=%E5%A4%9A%E7%BE%A4%E7%BB%84#)

其次，修改修改application.properties文件。多个群组使用,分隔。例如，假如存在1和2两个群组。

多群组将导出到相同的库中，如果想导出到不同库中，请另开工程。

当配置多群组时，表名将以群组id做前缀来区分，格式为：g1_tableName

配置如下：
```
system.groupId=1,2
```


#### 可视化监控程序安装和部署

##### 安装软件

首先，请安装docker，docker的安装可参考[docker安装手册](https://docker_practice.gitee.io/install/centos.html)
等docker安装成功后，请下载grafana：

```
docker pull grafana/grafana
```

如果你是使用sudo用户安装了docker，可能会提示『permission denied』的错误，建议执行:

```
sudo docker pull grafana/grafana
```

##### 启动grafana

```
docker run   -d   -p 3000:3000   --name=grafana   -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource"   grafana/grafana
```

grafana将自动绑定3000端口并自动安装时钟和Json的插件。

在application.properties中将grafana打开时，系统将会生成可视化json脚本 default_dashboard.json 文件，位于config目录下。

grafana安装并启动成功，通过访问[ip]:3000（本机则为localhost:3000）即可看到如下界面：
<br /> <br />
![](../../images/WeBankBlockchain-Data-Export/grafana_start.png)
<br /> <br />

输入账密admin/admin, 现在跳过即可进入主界面，添加导出数据库的mysql信息，如下位置：
<br /> <br />
![](../../images/WeBankBlockchain-Data-Export/grafana_index.png)
<br /> <br />

添加mysql成功后，可通过如下方式导入系统生成的default_dashboard.json文件，如下位置：
<br /> <br />
![](../../images/WeBankBlockchain-Data-Export/grafana_json.png)
<br /> <br />

导入成功后即可看到链的数据可视化情况，如下：
<br /> <br />
![](../../images/WeBankBlockchain-Data-Export/grafana_view.png)
<br /> <br />

更多关于Grafana的自定义配置和开发文档，可参考[Grafana官方文档](http://docs.grafana.org/guides/getting_started/)



###### 更多配置

更多配置参见[服务配置说明](./expertconfig.md)