## 服务配置说明

### 配置参数说明

组件中配置文件只有一个：./tools/config/resources/application.properties。该配置文件覆盖了数据导出组件所需的所有配置，并提供了详细的说明和样例，开发者可根据需求进行灵活配置。

#### Springboot服务配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| server.port | N | 启动WeBankBlockchain-Data-Export组件实例的服务端口 | 8082 | 5200 |

#### FISCO-BCOS节点配置

FISCO-BCOS节点配置用于配置服务连接的区块链节点，使得WeBankBlockchain-Data-Export服务能够访问连接节点，并通过该节点获取区块链网络上的数据。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.nodeStr | Y | 连接区块链节点的nodeStr，nodeName@[IP]:[PORT], 其中prot为channel port | node1@ip:8822 | - |


#### 数据库配置

数据导出组件最终会把区块链网络上的数据导出到数据存储介质中，目前版本仅支持MySQL，所以需要进行数据库配置。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.dbUrl | Y | 访问数据的URL | jdbc:mysql://[IP]:[PORT]/[DB]?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8 | - |
| system.dbUser | Y | 数据库用户名 | admin | - |
| system.dbPassword | Y | 数据库密码 | 123456 | - |
| system.contractName.[methodName or eventName].shardingNO | N | 合约数据分片数：数据库指定数据表的个数 | system.Rule.NewruleEvent.shardingNO = 3 | 1 |
| system.sys.[sysTableName].shardingNO | N | 系统数据分片数 | system.sys.BlockTxDetailInfo.shardingNO=5 | 1 |
| system.nameStyle | N | 数据库表名和字段命名规则，支持下划线命名和原始数据命名 | system.nameStyle=rawCase | underScoreCase |
| system.namePrefix | N | 数据库表字段命名前缀，默认为_ | system.namePrefix=_ | _ |
| system.namePostfix | N | 数据库表字段命名后缀，默认为空 | system.namePostfix=_ | 空 |
| system.tablePrefix | N | 数据库表名命名前缀，默认为空 | system.tablePrefix=_ | 空 |
| system.tablePostfix | N | 数据库表名命名后缀，默认为空 | system.tablePostfix=_ | 空 |
| system.dbIdentifierSplit | N | 是否开启自动裁剪过长的数据库表名，默认为false | system.dbIdentifierSplit=true | false |


其中**sysTableName**对应区块数据表和账户数据表，详情见 **数据存储模型** 章节。

#### 工程配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.group | Y | 同spring项目的group | com.example | - |
| system.contractPackName | Y | 编译智能合约所输入的包名 | com.webank.blockchain.wecredit.contracts | - |
| system.frequency | N | 所有method和event的抓取频率，默认几秒轮询一次 | 10 | 5 |

#### 线程池配置

在单机部署下，必须配置线程池参数。数据导出配置用于配置数据导出的频率、线程数及启动多线程条件等。当system.multiLiving=true时，配置文件不会生成线程池相关配置。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.multiLiving | Y | 关闭多活开关 | false | false |
| system.crawlBatchUnit | N | 线程处理单元：多线程任务模式下单个线程一次任务执行完成的区块数 | 100 | 100 |

#### 集群多活配置

在集群多活部署的方案中，必须设置集群多活的配置。集群必须通过zookeeper进行服务注册和任务分发。当system.multiLiving=false时，配置文件不会生成zookeeper相关配置。

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.multiLiving | Y | 启动多活开关 | true | false |
| regcenter.serverList | N | 注册中心服务器列表 | [ip1:2181;ip2:2181] | - |
| regcenter.namespace | N | 注册中心命名空间 | wecredit_bee | - |

#### elastic search配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| es.enabled | Y | 启动ES开关 | true | false |
| es.clusterName | N | 集群名称 | my-application | my-application |
| es.ip | N | es节点ip | 127.0.0.1 |  |
| es.port | N | es节点端口 | 9300 |  |

#### 其他高级配置

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| monitor.[contractName].[methodName/eventName].generated=false | N | 是否抓取特定合约中特定method或event的数据 | on/off | on |
| monitor.[contractName].[eventName].ignoreParam=XXX,XXX | N | 忽略特定合约特定event的特定字段不进行抓取 | xxx,xxx |  |
| length.[contractName].[methodName or eventName].paraName | N | 指定特定字段在数据库表中的长度,当设置值大于10240时，数据库类型将为text |  | 512 |
| button.swagger | N | 是否打开swagger功能，请务必在生成环境关闭此开关 | on/off | on |


### 配置工程

快速启动后，主要的基础配置都将会在配置中自动生成，无需额外配置。但是，基于已生成的配置文件，你可以继续按照需求进行深入的个性化高级配置，例如配置集群部署、分库分表、读写分离等等。

进入WeBankBlockchain-Data-Export-core的目录：

```
cd Data-Export/WeBankBlockchain-Data-Export-core

```

主要的配置文件位于src/main/resources目录下。其中，application.properties包含了除部分数据库配置外的全部配置。 application-sharding-tables.properties包含了数据库部分的配置。

注意： 当修改完配置文件后，需要重新编译代码，然后再执行，编译的命令如下：

```
bash gradlew clean bootJar

```

#### 导出数据范围的配置

配置文件位于 Data-Export/WeBankBlockchain-Data-Export-core/src/main/resources/application.properties

| 配置项 | 是否必输 | 说明 | 举例 | 默认值 |
| --- | --- | --- | --- | --- |
| system.startBlockHeight | N | 设置导出数据的起始区块号，优先以此配置为准 | 1000 | 0 |
| system.startDate | N | 设置导出数据的起始时间，例如设置导出2019年元旦开始上链的数据；如已配置startBlockHeight，以导出数据起始区块号为准。支持的数据格式包括：yyyy-MM-dd HH:mm:ss 或 yyyy-MM-dd 或 HH:mm:ss 或 yyyy-MM-dd HH:mm  或 yyyy-MM-dd  HH:mm:ss.SSS | 2019-01-01 | - |

#### 单节点部署的配置

在选择单节点配置后，以下配置会自动生成。
单节点任务调度的配置，分布式任务调度的配置默认位于 Data-Export/WeBankBlockchain-Data-Export-core/src/main/resources/application.properties

```
#### 当此参数为false时，进入单节点任务模式
system.multiLiving=false

#### 多线程下载的分片数量，当完成该分片所有的下载任务后，才会统一更新下载进度。
system.crawlBatchUnit=100
```

#### 集群部署的配置

多节点任务调度的配置，分布式任务调度的配置默认位于 Data-Export/WeBankBlockchain-Data-Export-core/src/main/resources/application.properties

```
#### 当此参数为true时，进入多节点任务模式
system.multiLiving=true

#### zookeeper配置信息，ip和端口
regcenter.serverList=ip:port
#### zookeeper的命名空间
regcenter.namespace=namespace

#### prepareTaskJob任务：主要用于读取当前区块链块高，将未抓取过的块高存储到数据库中。
#### cron表达式，用于控制作业触发时间
prepareTaskJob.cron=0/5 * * * * ?
### 分片总数量
prepareTaskJob.shardingTotalCount=1
#### 分片序列号和参数用等号分隔，多个键值对用逗号分隔,分片序列号从0开始，不可大于或等于作业分片总数
prepareTaskJob.shardingItemParameters=0=A

#### dataflowJob任务： 主要用于执行区块下载任务
dataflowJob.cron=0/5 * * * * ?
### 分片总数量
dataflowJob.shardingTotalCount=3
#### 分片序列号和参数用等号分隔，多个键值对用逗号分隔,分片序列号从0开始，不可大于或等于作业分片总数
dataflowJob.shardingItemParameters=0=A,1=B,2=C
```

数据库配置解析，数据库的配置默认位于 Data-Export/WeBankBlockchain-Data-Export-core/src/main/resources/application-sharding-tables.properties


#### 分库分表的配置

实践表明，当区块链上存在海量的数据时，导出到单个数据库或单个业务表会对运维造成巨大的压力，造成数据库性能的衰减。
一般来讲，单一数据库实例的数据的阈值在1TB之内，单一数据库表的数据的阈值在10G以内，是比较合理的范围。

如果数据量超过此阈值，建议对数据进行分片。将同一张表内的数据拆分到同个数据库的多张表。
```
spring.shardingsphere.datasource.names=ds
# 定义数据源ds属性        
spring.shardingsphere.datasource.ds.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.ds.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds.jdbc-url=jdbc:mysql://[ip]:3306/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.ds.username=
spring.shardingsphere.datasource.ds.password=

#将block_detail_info取模5路由到5张表
spring.shardingsphere.sharding.tables.block_detail_info.actual-data-nodes=ds.block_detail_info_$->{0..4}
spring.shardingsphere.sharding.tables.block_detail_info.table-strategy.inline.sharding-column=block_height
spring.shardingsphere.sharding.tables.block_detail_info.table-strategy.inline.algorithm-expression=block_detail_info_$->{block_height % 5}
spring.shardingsphere.sharding.tables.block_detail_info.key-generator-column-name=pk_id

 ```

 将同一张表内的数据拆分到多个数据库的多张表。

```

# 配置所有的数据源，如此处定义了ds,ds0,ds1 三个数据源，对应三个库
spring.shardingsphere.datasource.names=ds,ds0,ds1

# 设置默认的数据源
spring.shardingsphere.sharding.default-datasource-name=ds0

# 定义数据源ds
spring.shardingsphere.datasource.ds.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.ds.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds.jdbc-url=jdbc:mysql://[ip]:3306/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.ds.username=
spring.shardingsphere.datasource.ds.password=

# 定义数据源ds0
spring.shardingsphere.datasource.ds0.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.ds0.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds0.jdbc-url=jdbc:mysql://[ip]:3306/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.ds0.username=
spring.shardingsphere.datasource.ds0.password=

# 定义数据源ds1
spring.shardingsphere.datasource.ds1.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.ds1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.ds1.jdbc-url=jdbc:mysql://[ip]:3306/[db]?autoReconnect=true&useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.ds1.username=
spring.shardingsphere.datasource.ds1.password=

# 定义数据库默认分片的数量，此处分为2，以block_height取模2来路由到ds0或ds1
spring.shardingsphere.sharding.default-database-strategy.inline.sharding-column=block_height
spring.shardingsphere.sharding.default-database-strategy.inline.algorithm-expression=ds$->{block_height % 2}

# 定义block_detail_info的分表策略，以block_height取模2来路由到ds0的block_detail_info0或ds1的block_detail_info1
spring.shardingsphere.rules.sharding.tables.block_detail_info.actual-data-nodes=ds0.block_detail_info0,ds1.block_detail_info1
spring.shardingsphere.rules.sharding.tables.block_detail_info.table-strategy.inline.sharding-column=block_height
spring.shardingsphere.rules.sharding.tables.block_detail_info.table-strategy.inline.algorithm-expression=block_detail_info$->{block_height % 2}
spring.shardingsphere.rules.sharding.tables.block_detail_info.key-generator-column-name=pk_id

spring.shardingsphere.rules.sharding.tables.block_task_pool.actual-data-nodes=ds0.block_task_pool0,ds1.block_task_pool1
spring.shardingsphere.rules.sharding.tables.block_task_pool.table-strategy.inline.sharding-column=block_height
spring.shardingsphere.rules.sharding.tables.block_task_pool.table-strategy.inline.algorithm-expression=block_task_pool$->{block_height % 2}
spring.shardingsphere.rules.sharding.tables.block_task_pool.key-generator-column-name=pk_id

# 打印sql日志的开关
spring.shardingsphere.props.sql.show=true


```

#### 数据库读写分离的配置

数据库读写分离的主要设计目标是让用户无痛地使用主从数据库集群，就好像使用一个数据库一样。读写分离的特性支持往主库写入数据，往从库查询数据，从而减轻数据库的压力，提升服务的性能。

**注意**，本组件不会实现主库和从库的数据同步、主库和从库的数据同步延迟导致的数据不一致、主库双写或多写。

```
##### 配置一主两从的数据库

spring.shardingsphere.datasource.names=master,slave0,slave1

spring.shardingsphere.datasource.master.type=org.apache.commons.dbcp.BasicDataSource
spring.shardingsphere.datasource.master.driver-class-name=com.mysql.jdbc.Driver
spring.shardingsphere.datasource.master.url=jdbc:mysql://localhost:3306/master
spring.shardingsphere.datasource.master.username=
spring.shardingsphere.datasource.master.password=

spring.shardingsphere.datasource.slave0.type=org.apache.commons.dbcp.BasicDataSource
spring.shardingsphere.datasource.slave0.driver-class-name=com.mysql.jdbc.Driver
spring.shardingsphere.datasource.slave0.url=jdbc:mysql://localhost:3306/slave0
spring.shardingsphere.datasource.slave0.username=
spring.shardingsphere.datasource.slave0.password=

spring.shardingsphere.datasource.slave1.type=org.apache.commons.dbcp.BasicDataSource
spring.shardingsphere.datasource.slave1.driver-class-name=com.mysql.jdbc.Driver
spring.shardingsphere.datasource.slave1.url=jdbc:mysql://localhost:3306/slave1
spring.shardingsphere.datasource.slave1.username=
spring.shardingsphere.datasource.slave1.password=

spring.shardingsphere.masterslave.name=ms
spring.shardingsphere.masterslave.master-data-source-name=master
spring.shardingsphere.masterslave.slave-data-source-names=slave0,slave1

spring.shardingsphere.props.sql.show=true

```

#### 数据库读写分离+分库分表的配置


```

spring.shardingsphere.datasource.names=master,slave0
        
spring.shardingsphere.datasource.master.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.master.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.master.jdbc-url=jdbc:mysql://[ip]:3306/test0?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.master.username=
spring.shardingsphere.datasource.master.password=

spring.shardingsphere.datasource.slave0.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.slave0.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.slave0.jdbc-url=jdbc:mysql://[ip]:3306/test1?useSSL=false&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8
spring.shardingsphere.datasource.slave0.username=
spring.shardingsphere.datasource.slave0.password=

spring.shardingsphere.sharding.master-slave-rules.ds0.master-data-source-name=master
spring.shardingsphere.sharding.master-slave-rules.ds0.slave-data-source-names=slave0

spring.shardingsphere.sharding.tables.activity_activity.actual-data-nodes=ds0.block_tx_detail_info$->{0..1}
spring.shardingsphere.sharding.tables.activity_activity.table-strategy.inline.sharding-column=block_height
spring.shardingsphere.sharding.tables.activity_activity.table-strategy.inline.algorithm-expression=block_tx_detail_info$->{block_height % 2}


```


