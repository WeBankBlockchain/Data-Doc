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

得到工程代码，WeBankBlockchain-Data-Export的工程使用gradle进行构建。

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
└── settings.gradle

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

修改application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要配置的：

```
### 数据导出支持以下三种方式:
### 1, Channel
### 2, JsonRPC
### 3, Data-Stash
### 选择其中一种方式配置即可，默认Channel方式

# Channel方式启动，需配置证书
## GROUP_ID必须与FISCO-BCOS中配置的groupId一致, 多群组以,分隔，如1,2
system.groupId=1
# 节点的IP及通讯端口、组号。 
##IP为节点运行的IP，PORT为节点运行的channel_port，默认为20200
system.nodeStr=127.0.0.1:20200

### 数据库的信息，暂时只支持mysql； serverTimezone 用来设置时区
### 请确保在运行前创建对应的database，如果分库分表，则可配置多个数据源，如system.db1.dbUrl=\system.db1.user=\system.db0.password=
system.db0.dbUrl=jdbc:mysql://127.0.0.1:3306/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db0.user=root
system.db0.password=123456

```

数据导出除支持上述的Channel方式导出数据外，还支持[JSON-RPC方式](./expertconfig.html#json-rpc)和[数据仓库方式](./expertconfig.html#id3)

其中多群组数据导出，参照[多群组数据导出](./expertconfig.html#id11)

##### 配置合约

将合约对应的abi和binary文件分别放置到config/abi和config/bin目录中。

两个目录中包含了一个HelloWorld合约的abi和bin文件，使用时请按需删除。

合约bin和binary的获取参考[编译智能合约](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/tutorial/sdk_application.html#id6)


##### 可视化配置

在application.properties中将grafana打开时，将在config目录下生成可视化脚本，默认关闭，打开配置如下:
```
system.grafanaEnable=true

```


#### 创建数据库

参考[连接和创建数据库](./appendix.html#mysql)


#### 运行程序

```eval_rst
.. note::
        本工程默认使用gradle wrapper来实施构建。在必要时，可在start.sh时添加-c的编译选项，指定使用本机的gradle来进行代码编译。

            - 如果运行程序以后无法正常下载gradle wrapper，可自行安装gradle软件，可参考 `官网安装教程 <https://gradle.org/install/>`_ 。       
            - 如果本机已经安装了符合要求的gradle软件，则可以使用`./start.sh -c gradle`选项来指定编译方式，使用本机安装的gradle来实施构建。
```

```
chmod +x start.sh
bash start.sh
```

```eval_rst
.. important::
    请务必按照以上命令操作，**请勿使用sudo命令来操作**，否则会导致Gradlew没有权限，导致导出数据失败。
```

更多运行方式参照[运行方式说明](./expertconfig.html#id13)



#### 检查运行状态及退出

##### 检查程序进程是否正常运行

```
ps -ef |grep Data-Export
```

如果看到如下信息，则代表进程执行正常：

```
app   21980 24843  0 15:23 pts/3    00:00:44 java -jar Data-Export*.jar
```

##### 检查程序是否已经正常执行

当你看到程序运行，并在最后出现以下字样时，则代表运行成功：

```
select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
select blockheigh0_.pk_id as pk_id1_2_, blockheigh0_.block_height as block_he2_2_, blockheigh0_.event_name as event_na3_2_, blockheigh0_.depot_updatetime as depot_up4_2_ from block_height_info blockheigh0_ where blockheigh0_.event_name=?
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

##### 检查数据是否已经正常产生


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

**停止导入程序**

```
bash stop.sh
```

恭喜您，到以上步骤，您已经完成了数据导出组件的安装和部署。如果您还需要额外获得可视化的监控页面，请参考下述可视化安装和部署。


#### 更多配置

除上述功能外，还支持以下功能：

- [可视化监控安装](./view.html#id3)

- [数据表个性化](./expertconfig.html#id4)

- [ES存储](./expertconfig.html#es)

- [集群多活](./expertconfig.html#id7)

- [分库分表](./expertconfig.html#id12)

- [工程配置](./expertconfig.html#id6)

- [合约字段个性化配置](./expertconfig.html#id9)

更多配置参见[服务配置说明](./expertconfig.md)