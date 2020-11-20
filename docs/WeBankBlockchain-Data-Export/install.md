## 快速开始

### 1. 前置依赖

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


### 2 部署步骤

#### 2.1 获取工程

##### 2.1.1 代码拉取

```shell
git clone https://github.com/WeBankBlockchain/WeBankBlockchain-Data-Export.git

```

得到工程代码，WeBankBlockchain-Data-Export的工程使用gradle进行构建，是一个使用gradle进行多工程构建的SpringBoot工程。

```
├── ChangeLog.md
├── LICENSE
├── README.md
├── tools
├── WeBankBlockchain-Data-Export-codegen
├── WeBankBlockchain-Data-Export-common
├── WeBankBlockchain-Data-Export-core
├── WeBankBlockchain-Data-Export-db
├── WeBankBlockchain-Data-Export-extractor
├── WeBankBlockchain-Data-Export-parser
├── build.gradle
├── gradle
├── gradlew
├── gradlew.bat
├── libs
├── settings.gradle
└── src

```

其中各个子工程的说明如下：

WeBankBlockchain-Data-Export-codegen 数据导出代码生成功能

WeBankBlockchain-Data-Export-core是运行任务的主工程。

WeBankBlockchain-Data-Export-common 公共类库。

WeBankBlockchain-Data-Export-db 数据库相关的功能。

WeBankBlockchain-Data-Export-extractor 区块抽取相关的功能。

WeBankBlockchain-Data-Export-parser 区块解析相关的功能。


其中build.gradle为gradle的构建文件，tools/config/contract目录存放了合约编译为Java的文件，tools/config/resources下面存放了配置文件


##### 2.1.2 进入安装路径

```shell
cd WeBankBlockchain-Data-Export/tools

```

tools目录如下：
```
├── tools
│   ├── config
│   │   ├── contract
│   │   │   └── HelloWorld.java
│   │   └── resources
│   │       └── application.properties
│   │       └── web3j.def
│   └── build_export.sh
```

#### 2.2 配置工程

##### 2.2.1 配置合约文件

找到你的业务工程（你要导出数据的那条区块链中，往区块链写数据的工程），复制合约产生的Java文件：请将Java文件**复制到./tools/config/contract目录**下（请先删除目录结构中的合约示例HelloWorld.java文件）。

如果你的业务工程并非Java工程，那就先找到你所有的合约代码。不清楚如何将Solidity合约生成为Java文件，请参考： [利用控制台将合约代码转换为java代码](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/console.html)


##### 2.2.2 配置证书文件

将SDK证书拷贝到 **./tools/config/resources目录**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./tools/config/resources
```

##### 2.2.3 配置应用

修改application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要修改的，否则跑不起来：

```
# 节点的IP及通讯端口、组号。 
##IP为节点运行的IP，PORT为节点运行的channel_port，默认为20200。
system.nodeStr=[IP]:[PORT]
## GROUP_ID必须与FISCO-BCOS中配置的groupId一致。
system.groupId=[GROUP_ID]

# 数据库的信息，暂时只支持mysql； serverTimezone 用来设置时区
## 请确保在运行前创建对应的database
system.dbUrl=jdbc:mysql://[IP]:[PORT]/[database]?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.dbUser=[user_name]
system.dbPassword=[password]

# 合约Java文件的包名
system.contractPackName=[编译Solidity合约时指定的包名]

# elastic serach 配置（如果需要的话可进行配置）
##是否开启ES存储
es.enabled=false
##ES集群名称
es.clusterName=my-application
es.ip=[ip]
es.port=9300
```

##### 2.2.4 创建数据库

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

##### 2.2.5 创建Elasticsearch

需要ES存储时，需先安装ES, 参考[ES部署](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/index.html)

#### 2.3 运行程序


##### 2.3.1 选择一：直接在本机运行

```
chmod +x build_export.sh
bash build_export.sh
## 还可以指定数据导出程序的版本，例如
## ./build_export.sh -v 1.3.0
```

请注意:请务必按照以上命令操作，**切莫使用sudo命令来操作**，否则会导致Gradlew没有权限，导致depot数据失败。

##### 2.3.2 选择二：本机编译，复制执行包到其他服务器上运行

```
chmod +x build_export.sh
bash build_export.sh
## 还可以指定数据导出程序的版本，例如
## ./build_export.sh -e build -v 1.3.0
```

请将此工程下的./WeBankBlockchain-Data-Export/WeBankBlockchain-Data-Export-core/dist文件夹复制到其他服务器上，并执行：

```
chmod +x *.sh
bash start.sh
tail -f *.log
```

##### 2.3.3 选择三：本机编译，复制执行包到其他服务器，使用supervisor来启动。

使用supervisor来守护和管理进程，supervisor能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能自动重启。
它是通过fork/exec的方式把这些被管理的进程当作supervisor的子进程来启动，这样只要在supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去即可。也实现当子进程挂掉的时候，父进程可以准确获取子进程挂掉的信息的，可以选择是否自己启动和报警。
supervisor还提供了一个功能，可以为supervisord或者每个子进程，设置一个非root的user，这个user就可以管理它对应的进程。
编译生成代码的部署同2.2.3.2

使用supervisor来安装与部署的步骤请参阅[附录6](appendix.html#supervisor)

#### 2.4 检查运行状态及退出

##### 2.4.1 检查程序进程是否正常运行

```
ps -ef |grep Data-Export
```

如果看到如下信息，则代表进程执行正常：

```
app   21980 24843  0 15:23 pts/3    00:00:44 java -jar WeBankBlockchain-Data-Export-core1.3.1.jar
```

##### 2.4.2 检查程序是否已经正常执行

当你看到程序运行，并在最后出现以下字样时，则代表运行成功：

```
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
Hibernate: select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
```

还可以通过以下命令来查看区块的同步状态：

```
tail -f dataexport-core.log| grep "sync block"
```

当看到以下滚动的日志时，则代表区块同步状态正常，开始执行下载任务。

```
 $ tail -f dataexport-core.log| grep "sync block"
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

##### 2.4.3 检查数据是否已经正常产生


###### 2.4.3.1 DB数据检查

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

###### 2.4.3.2 ES数据检查

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

##### 2.4.4 停止导入程序

```
bash stop.sh
```

恭喜您，到以上步骤，您已经完成了数据导出组件的安装和部署。如果您还需要额外获得可视化的监控页面，请参考3.3章节。


#### 2.5 支持多群组数据导出
首先，请配置FISCO BCOS的多群组，详情可参考[FISCO BCOS多群组部署](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/group_use_cases.html?highlight=%E5%A4%9A%E7%BE%A4%E7%BB%84#)

其次，修改修改application.properties文件。多个群组使用,分隔。例如，假如存在1和2两个群组。
```
system.groupId=1,2
```
无需其他更多的配置，其他和群组相关的配置会自动生成。例如：
- server.port，系统服务监听端口，根据配置，按照群组自动累加
- logging.file，日志文件名称，根据群组名，自动拼装，如群组1的日志文件名为dataexport-core-1.log
- system.dbUrl，数据库名。数据库名按照配置的数据库名，自动组装拼接。例如配置的database名称为bee，则群组1的数据库名自动为bee_g1.

再次，按照之前的步骤启动build程序。
```
./build_export.sh -e build
```
在dist目录下，会自动构建运行的程序和配置。同时，会自动生成start_all_groups.sh的脚本。

在启动服务之前，请按照之前application.properties的配置中的system.dbUrl，创建对应的数据库。数据库名为所配置的数据库名加上群组名。
假如所配置的数据库名为bee，群组为1和2，则需要手动创建好数据库bee_g1和bee_g2.
```
sql_admin > 
create database bee_g1;
create database bee_g2;
```

启动所有群组：
```
./start_all_groups.sh
```

关闭所有群组的服务：
```
./stop.sh
```

#### 2.6 注意事项

在生成的工程中，我们使用了Hibernate auto-ddl 的特性来自动创建数据库表，该特性仅供提供快速的演示，但请勿使用该特性上线；否则可能会造成生产系统的安全隐患。

你可以修改WeBankBlockchain-Data-Export-core/src/main/resources/appliction.properties:

```

spring.jpa.properties.hibernate.hbm2ddl.auto=none

```

### 3. 可视化监控程序安装和部署

#### 3.1 安装软件

首先，请安装docker，docker的安装可参考[docker安装手册](https://docker_practice.gitee.io/install/centos.html)
等docker安装成功后，请下载grafana：

```
docker pull grafana/grafana
```

如果你是使用sudo用户安装了docker，可能会提示『permission denied』的错误，建议执行:

```
sudo docker pull grafana/grafana
```

#### 3.2 启动grafana

```
docker run   -d   -p 3000:3000   --name=grafana   -e "GF_INSTALL_PLUGINS=grafana-clock-panel,grafana-simple-json-datasource"   grafana/grafana
```

grafana将自动绑定3000端口并自动安装时钟和Json的插件。

#### 3.3 登录grafana界面

直接使用浏览器访问：http://your_ip:3000/，请注意使用你机器的IP替换掉your_ip，默认的用户名和密码为admin/admin

#### 3.4 添加MySQL数据源

在正常登录成功后，如图所示，选择左边栏设置按钮，点击『Data Sources』，选择『MySQL』数据源，随后按照提示的页面，配置 Host， Database， User 和 Password等。

![[添加步骤]](../../images/WeBankBlockchain-Data-Export/add_datasource.png)

#### 3.5 导入Dashboard模板

WeBankBlockchain-Data-Export-codegen会自动生成数据的dashboard模板，数据的路径位于：WeBankBlockchain-Data-Export/WeBankBlockchain-Data-Export-core/src/main/scripts/grafana/default_dashboard.json，请点击左边栏『+』，选择『import』，点击绿色按钮『Upload.json File』,选择刚才的WeBankBlockchain-Data-Export/src/main/scripts/grafana/default_dashboard.json文件，最后，点击『import』按钮。

![[导入步骤]](../../images/WeBankBlockchain-Data-Export/import_json.png)

如果导入成功，dashboards下面会出现『FISCO-BCOS区块链监控视图』，您可以选择右上方的时间按钮来选择和设置时间范围及刷新时间等。您也可以选中具体的页面组件进行编辑，自由地移除或挪动组件的位置，达到更好的使用体验。

更多关于Grafana的自定义配置和开发文档，可参考[Grafana官方文档](http://docs.grafana.org/guides/getting_started/)

### 4. 开启可视化的API文档和功能性测试

[WeBankBlockchain-Data-Export](https://github.com/WeBankFinTech/WeBankBlockchain-Data-Export/tree/master)默认集成了swagger的插件，支持通过可视化的控制台来发送交易、生成报文、查看结果、调试交易等。

![[swagger控制台]](../../images/WeBankBlockchain-Data-Export/swagger.png)

**请注意，swagger插件仅推荐在开发或测试环境调试使用，在正式上生产环境时，请关闭此插件。 **

如果未在monkey工程中配置打开swagger选项，则默认关闭，需要在配置文件中打开。

打开方法：

打开config/resources/application.properties，添加：
```
button.swagger=on
```


#### 4.1 查看API文档：

请在你的浏览器打开此地址：

> http://your_ip:port/swagger-ui.html

例如，当你在本机运行了[WeBankBlockchain-Data-Export](https://github.com/WeBankFinTech/WeBankBlockchain-Data-Export/tree/master)，且未修改默认的5200端口，则可以访问此地址：

> http://localhost:5200/swagger-ui.html

此时，你可以看到上述页面，可以看到页面主要包括了http请求页面和数据模型两部分。

#### 4.2 使用swagger发送具体的交易：

选择点击对应的http请求集，可以点开相关的http请求。此时，你可以选择点击“try it out”，手动修改发送的Json报文，点击“Excute”按钮，即可发送并查收结果。

我们以查询区块信息为例，如下列图所示：
![[选择请求]](../../images/WeBankBlockchain-Data-Export/swag_test1.png)
![[编辑报文]](../../images/WeBankBlockchain-Data-Export/swag_test2.png)
![[查收结果]](../../images/WeBankBlockchain-Data-Export/swag_test3.png)
