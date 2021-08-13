## 服务配置说明

### 配置参数说明

组件中配置文件只有一个：./tools/config/resources/application.properties。该配置文件覆盖了数据导出组件所需的所有配置，并提供了详细的说明和样例，开发者可根据需求进行灵活配置。

#### Springboot服务配置

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| server.port | N | 启动WeBankBlockchain-Data-Export组件实例的服务端口 | 8082 | 5200 |

#### FISCO-BCOS节点配置

FISCO-BCOS节点配置用于配置服务连接的区块链节点，使得WeBankBlockchain-Data-Export服务能够访问连接节点，并通过该节点获取区块链网络上的数据。
连接区块链节点包括两种方式：channel和JSON-RPC方式

##### channel方式配置说明如下：

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.nodeStr | Y | 连接区块链节点的nodeStr,nodeName@[IP]:[PORT], 其中prot为channel port | - | - |
| system.groupId | Y | 群组id，多群组以,分隔 | - | 1 |
| system.certPath | Y | 证书路径 | - | ./config |

##### JSON-RPC方式配置说明如下：

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.rpcUrl | Y | 连接区块链节点的rpc url, http://[IP]:[PORT], 其中prot为rpc port | http://localhost:8546 | - |
| system.groupId | Y | 群组id，多群组以,分隔 | 1 | 1 |
| system.cryptoTypeConfig | Y | 链密钥类型 | 0-ECDSA，1-gm | 0 |

#### 数据仓库连接配置

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.jdbcUrl | Y | 数据仓库jdbc连接配置，格式：jdbc:mysql://[ip]:[port]/[database] | http://localhost:3306/stash | - |
| system.user | Y | 用户名 | - | - |
| system.password | Y | 密码 | - | - |
| system.cryptoTypeConfig | Y | 链密钥类型 | 0-ECDSA，1-gm | 0 |


#### 数据库配置

数据导出组件最终会把区块链网络上的数据导出到数据存储介质中，支持MySQL，所以需要进行数据库配置。

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.db0.dbUrl | Y | 访问数据的URL| jdbc:mysql://[IP]:[PORT]/[DB]?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8 | - |
| system.db0.dbUser | Y | 数据库用户名 | admin | - |
| system.db0.dbPassword | Y | 数据库密码 | 123456 | - |
| system.namePrefix | N | 合约导出表字段前缀设置，只针对method、event表中变量字段,字段名长度应小于64个字符 |- | 空 |
| system.namePostfix | N | 合约导出表字段后缀设置，只针对method、event表中变量字段,字段名长度应小于64个字符  | - | 空 |
| system.tablePrefix | N | 数据导出表名前缀设置,表名长度应小于64个字符 | -  |空 |
| system.tablePostfix | N |  数据导出表名后缀设置,表名长度应小于64个字符 | - | 空 |
| system.db.autoCreateTable | N |  自动建表 | - | true |
| system.db.sharding | N |  开启分库分表 | - | false |
| system.db.shardingNumberPerDatasource | N |  分表数目 | - | 0 |
| system.paramSQLType | N | 指定数据表字段类型，针对事件或方法字段，多个配置已竖杠字符分隔，contractName.methodName/eventName.paramName.sqlType| - | HelloWorld.set.name.text |

上述配置system.paramSQLType中，指定字段不包括块和交易等基础字段，基础字段参考[存储模型](./model.html#id9)


#### 合约配置

数据导出组件最终会把区块链网络上的数据导出到数据存储介质中，支持MySQL，所以需要进行数据库配置。

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.abiPath | Y | 合约abi地址 | - | ./config/abi |
| system.binPath | Y | 合约bin地址 | - | ./config/bin |


#### 工程配置

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.frequency | N | 所有method和event的抓取频率，默认几秒轮询一次 | 10 | 5 |
| system.crawlBatchUnit | N | 一次任务执行完成的区块数 | 100 | 500 |
| system.startBlockHeight | N | 开始区块高度 | - | 0 |
| system.startDate | N | 从大于指定时间开始导出，注意本地utc时间应为当前时区时间 | 2021-03-04 | - |

```eval_rst
.. note::
      上述 **system.startBlockHeight** 和 **system.startDate** 同时配置将优先读取前者指定块高，后者会不生效。
```


#### 集群多活配置

在集群多活部署的方案中，必须设置集群多活的配置。集群必须通过zookeeper进行服务注册和任务分发。

| 配置项 | 是否必须 | 说明 | 类型 | 默认值 |
| --- | --- | --- | --- | --- |
| system.multiLiving | Y | 启动多活开关 | boolean | false |
| regcenter.serverList | N | 注册中心服务器列表 | [ip1:2181;ip2:2181] | - |
| regcenter.namespace | N | 注册中心命名空间 | wecredit_bee | - |
| zookeeperServiceLists | N | zk服务节点列表(,分隔),格式：[IP]:[port],[IP]:[port] | string | null |
| zookeeperNamespace | N | zk命名空间(,分隔) | string | null |
| prepareTaskJobCron | N | 任务准备job定时配置 ，主要用于读取当前区块链块高，将未抓取过的块高存储到数据库中| string | "0/"+ frequency + " * * * * ?" |
| dataFlowJobCron | N | 任务分片执行job定时配置，主要用于执行区块下载任务 | string |"0/"+ frequency + " * * * * ?" |
| dataFlowJobItemParameters | N | 分片序列号和参数用等号分隔，多个键值对用逗号分隔,分片序列号从0开始，不可大于或等于作业分片总数 | string | 如 "0=A,1=B,2=C,3=D,4=E,5=F,6=G,7=H" |
| dataFlowJobShardingTotalCount | N | 任务分片数目 | int | 8 |

#### elastic search配置

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| es.enabled | Y | 启动ES开关 | true | false |
| es.clusterName | N | 集群名称 | my-application | my-application |
| es.ip | N | es节点ip | 127.0.0.1 |  |
| es.port | N | es节点端口 | 9300 |  |

#### 可视化配置

开启可视化配置会生成grafana可视化json脚本，可在启动grafana后导入该脚本，即可完成可视化。

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.grafanaEnable | N | 是否开启可视化 | true | false |


#### 其他高级配置

| 配置项 | 是否必须 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.generatedOffStr | N | 指定事件或方法不导出，多个以竖杠字符分隔分隔，[contractName.methodName/eventName,methodName or eventName,...]|  HelloWorld.set| - |
| system.ignoreParam | N | 指定事件或方法中字段不导出，多个以竖杠字符分隔分隔，[contractName.methodName/eventName.paramName1,paramName2]| HelloWorld.set.n | - |
| system.dataTypeBlackList | N | 指定数据类型表不导出，多个以,分隔（docker方式启动目前不支持该配置）| block_detail_info,block_raw_data, | - |
| system.ignoreBasicDataTableParams | N | 原始数据表指定字段导出过滤，多表之间以竖杠字符分隔，（docker方式启动目前不支持该配置）| tx_raw_data.from,to | - |

system.dataTypeBlackList配置支持以下数据类型配置：
```
block_detail_info
block_raw_data
block_tx_detail_info
tx_raw_data
tx_receipt_raw_data
deployed_account_info
contract_info
event
method

配置例子如：system.dataTypeBlackLists=block_detail_info,block_tx_detail_info
```

上述中system.ignoreBasicDataTableParams配置支持以下基础数据表配置：
```
block_raw_data表支持以下字段过滤：
db_hash,extra_data,gas_limit,gas_used,logs_bloom,parent_hash,receipts_root,sealer,sealer_list,signature_list,state_root,transaction_list,transactions_root

tx_raw_data表支持以下字段过滤：
from,gas,gas_price,input,nonce,value,to

tx_receipt_raw_data表支持以下字段过滤：
from,gas_used,logs,input,message,output,logs_bloom,root,to,tx_index,tx_proof,receipt_proof

配置例子如：system.ignoreBasicDataTableParams=tx_raw_data.from,to|block_raw_data.db_hash,gas_limit
```

### 配置操作说明

#### 多群组数据导出

首先，请配置FISCO BCOS的多群组，详情可参考[FISCO BCOS多群组部署](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/manual/group_use_cases.html?highlight=%E5%A4%9A%E7%BE%A4%E7%BB%84#)

其次，修改application.properties文件。多个群组使用,分隔。例如，假如存在1和2两个群组。

多群组将导出到相同的库中，表名将以群组id做前缀来区分，格式为：g1_tableName

配置如下：

```
system.groupId=1,2
```

#### 分库分表配置

数据源配置中，在分库分表时可配置多个，以db0、db1..区分，如system.db0.dbUrl、system.db1.dbUrl...按组递增排列

数据库路由规则为： **block_height(区块高度) % 配置数据库数目**

表路由规则为： **block_height(区块高度) % 表分片数目(shardingNumberPerDatasource)**

分库分表所需配置如下：
```
system.db0.dbUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db0.user=
system.db0.password=

system.db1.dbUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db1.user=
system.db1.password=

system.db2.dbUrl=jdbc:mysql://[ip]:[port]/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
system.db2.user=
system.db2.password=

system.db.sharding=true
system.db.shardingNumberPerDatasource=3
```

#### 运行方式

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


#### ES部署配置

需要ES存储时，需先安装ES，安装ES可通过docker和官网方式安装

##### docker安装

```
//创建数据挂载目录
mkdir -p /mydata/elasticsearch/data
chmod -R 777 /mydata/elasticsearch/
//拉取es镜像
docker pull elasticsearch:7.8.0
//启动es容器
docker run --name elasticsearch -d -e ES_JAVA_OPTS="-Xms128m -Xmx128m" -e "discovery.type=single-node" -p 9200:9200 -p 9300:9300  -v  /mydata/elasticsearch/data:/usr/share/elasticsearch/data -d  elasticsearch:7.8.0
```

##### 参考官网安装

可参考官网[ES 7.X版本部署](https://www.elastic.co/guide/en/elasticsearch/reference/7.x/index.html)

安装完成后，可通过以下命令查看ES安装信息
```
curl 127.0.0.1:9200/
```

结果如下：
```
{
  "name" : "78a052fcba87",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "cJoABrm_RaicXPXQKEYNdw",
  "version" : {
    "number" : "7.8.0",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "757314695644ea9a1dc2fecd26d1a43856725e65",
    "build_date" : "2020-06-14T19:35:50.234439Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```


配置参考[ES配置](./expertconfig.html#elastic-search)

**启动成功后ES数据检查**

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
