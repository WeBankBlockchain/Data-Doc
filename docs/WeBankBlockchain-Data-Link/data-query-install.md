### data-query启动

#### 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |备注|
| --- | --- | --- |
| FISCO-BCOS | >= 2.0， 1.x版本请参考V0.5版本 |
| Bash | 需支持Bash（理论上来说支持所有ksh、zsh等其他unix shell，但未测试）|
| Java | JDK[1.8] | |
| Git | 下载的安装包使用Git | |
| MySQL | >= mysql-community-server[5.7] | |


#### 部署步骤

##### 节点配置

FISCO BCOS节点在配置scalable存储模式时，对于本地经裁剪后缺少的数据，将通过代理data-query访问“数据仓库”数据源进行获取。

###### 模式启用

- 设置群组的ini配置文件中`[storage].type=scalable`来选择链的存储模式为`scalable`。
- 设置群组的ini配置文件中`[storage].binary_log=true`来启用binlog。如用户使用build_chain脚本搭链，并选择scalable存储模式，配置文件会自动开启binlog。
- 参考[说明](./data_governance.html#amdb-proxy)进行amdb-proxy配置，其中设置amdb-proxy访问的数据源为“数据仓库”生成的数据库。

###### 文件组织

节点使用scalable存储模式后，其数据目录内容如下：

- `nodeX/data/groupX/block/Scalable/blocksDB/`下分文件夹存储区块数据。每个文件夹为独立的DB实例，用DB记录的首个块高来进行文件夹命名。用户在配置了“数据仓库”和amdb-proxy实现数据备份归档后，较旧（较低块高）的子文件夹**允许删除**。
- `nodeX/data/groupX/block/Scalable/state`存储整体的状态数据，该文件夹**不可删除**。

###### binlog

binlog文件记录了每个区块的每个交易对区块链状态的修改结果。binlog机制的作用在于：

1. 提供了区块维度的数据操作结果的记录；
2. 节点可通过binlog文件而非通过原有的拉取区块重放交易的方式来恢复数据；
3. binlog文件为“数据仓库”的快照构建提供数据来源。

用户可通过设置群组的ini配置文件中`[storage].binary_log=true`来启用binlog（binlog默认关）。开启binlog后，`nodeX/data/groupX/BinaryLogs/`的目录如下。每个binlog文件以其记录的首个区块内容进行命名，且按从小到大的顺序来记录区块。如下图中，文件`18.binlog`记录的区块为块高18到块高29。

```bash
├── 0.binlog
├── 18.binlog
├── 30.binlog
└── 32.binlog
```

##### 获取工程

###### 代码拉取

```shell
https://github.com/WeBankBlockchain/Data-Link.git

```

```eval_rst
.. note::
    - 如果因为网络问题导致长时间无法下载，请尝试：git clone https://gitee.com/WeBankBlockchain/Data-Link.git
```



###### 进入安装路径

```shell
cd datalink-query/tools
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
    - **config为配置文件目录，使用channel方式连接区块链时，可将证书放至该目录。**
    - 运行日志保存在./tools/log目录下

```
##### 配置工程


###### 配置文件设置

修改application.properties文件：该文件包含了所有的配置信息。以下配置信息是必须要配置的：

```
system.groupId=1
system.nodeStr=127.0.0.1:20200
system.topic=DB
system.ficso.certPath=./config

spring.datasource.url=jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8
spring.datasource.username=
spring.datasource.password=

```


###### 配置证书文件

连接链节点时，需配置证书或证书路径。

将链SDK证书拷贝到 **./tools/config**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./tools/config/
```


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



#### 检查运行状态及退出

##### 检查程序进程是否正常运行

```
ps -ef |grep Data-
```

如果看到如下信息，则代表进程执行正常：

```
app   21980 24843  0 15:23 pts/3    00:00:44 java -jar Data-Export*.jar
```

