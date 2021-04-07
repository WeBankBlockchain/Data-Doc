## 服务配置说明

### 配置参数说明

组件中配置文件只有一个：./tools/config/resources/application.properties。该配置文件覆盖了数据导出组件所需的所有配置，并提供了详细的说明和样例，开发者可根据需求进行灵活配置。

#### Springboot服务配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| server.port | N | 启动WeBankBlockchain-Data-Export组件实例的服务端口 | 8082 | 5200 |

#### FISCO-BCOS节点配置

FISCO-BCOS节点配置用于配置服务连接的区块链节点，使得WeBankBlockchain-Data-Export服务能够访问连接节点，并通过该节点获取区块链网络上的数据。
连接区块链节点包括两种方式：channel和JSON-RPC方式

##### channel方式配置说明如下：

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.nodeStr | Y | 连接区块链节点的nodeStr,nodeName@[IP]:[PORT], 其中prot为channel port | - | - |
| system.groupId | Y | 群组id | - | 1 |
| system.certPath | Y | 证书路径 | - | ./config |

##### JSON-RPC方式配置说明如下：

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.rpcUrl | Y | 连接区块链节点的rpc url, http://[IP]:[PORT], 其中prot为rpc port | http://localhost:8546 | - |
| system.groupId | Y | 群组id | 1 | 1 |
| system.cryptoTypeConfig | Y | 链密钥类型 | 0-ECDSA，1-gm | 0 |

#### 数据仓库连接配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.jdbcUrl | Y | 数据仓库jdbc连接配置，格式：jdbc:mysql://[ip]:[port]/[database] | http://localhost:3306/stash | - |
| system.user | Y | 用户名 | - | - |
| system.password | Y | 密码 | - | - |
| system.cryptoTypeConfig | Y | 链密钥类型 | 0-ECDSA，1-gm | 0 |


#### 数据库配置

数据导出组件最终会把区块链网络上的数据导出到数据存储介质中，支持MySQL，所以需要进行数据库配置。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.db0.dbUrl | Y | 访问数据的URL| jdbc:mysql://[IP]:[PORT]/[DB]?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8 | - |
| system.db0.dbUser | Y | 数据库用户名 | admin | - |
| system.db0.dbPassword | Y | 数据库密码 | 123456 | - |
| system.namePrefix | N | 合约导出表字段前缀设置，只针对method、event表中变量字段  |- | 空 |
| system.namePostfix | N | 合约导出表字段后缀设置，只针对method、event表中变量字段 | - | 空 |
| system.tablePrefix | N | 数据导出表名前缀设置 | -  |空 |
| system.tablePostfix | N |  数据导出表名前缀设置 | - | 空 |
| system.db.autoCreateTable | N |  自动建表 | - | true |
| system.db.sharding | N |  开启分库分表 | - | false |
| system.db.shardingNumberPerDatasource | N |  分表数目 | - | 0 |
| system.paramSQLType | N |  指定数据表字段类型，针对事件或方法字段，多个配置已竖杠字符分隔，[contractName.methodName/eventName.solidityParamName.paramType] | - | HelloWorld.set.name.text |

##### 分库分表

上述数据源配置中，在分库分表时可配置多个，以db0、db1..区分，如system.db0.dbUrl、system.db1.dbUrl...按组递增排列
配置如下：
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


#### 合约配置

数据导出组件最终会把区块链网络上的数据导出到数据存储介质中，支持MySQL，所以需要进行数据库配置。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.abiPath | Y | 合约abi地址 | - | ./config/abi |
| system.binPath | Y | 合约bin地址 | - | ./config/bin |


#### 工程配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.frequency | N | 所有method和event的抓取频率，默认几秒轮询一次 | 10 | 5 |
| system.crawlBatchUnit | N | 一次任务执行完成的区块数 | 100 | 1000 |
| system.startBlockHeight | N | 开始区块高度 | - | 0 |
| system.startDate | N | 从指定区块时间开始导出 | 2021-03-04 | - |

#### 集群多活配置

在集群多活部署的方案中，必须设置集群多活的配置。集群必须通过zookeeper进行服务注册和任务分发。

| 配置项 | 是否必输 | 说明 | 类型 | 默认值 |
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

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| es.enabled | Y | 启动ES开关 | true | false |
| es.clusterName | N | 集群名称 | my-application | my-application |
| es.ip | N | es节点ip | 127.0.0.1 |  |
| es.port | N | es节点端口 | 9300 |  |

#### 可视化配置

开启可视化配置会生成grafana可视化json脚本，可在启动grafana后导入该脚本，即可完成可视化。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.grafanaEnable | N | 是否开启可视化 | true | false |


#### 其他高级配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.generatedOffStr | N | 指定事件或方法不导出，多个以竖杠字符分隔分隔，[contractName.methodName/eventName,methodName or eventName,...] |  HelloWorld.set.nameA,nameB | - |
| system.ignoreParam | N | 指定事件或方法中字段不导出，多个以竖杠字符分隔分隔，[contractName.methodName/eventName.javaParamName,javaParamName] | HelloWorld.set.name.text | - |
