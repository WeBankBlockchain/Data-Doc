## SDK-API详解

**SDK提供接口如下：**
```
//创建数据导出执行器DataExportExecutor，导出配置采用默认配置
DataExportExecutor create(ExportDataSource dataSource, ChainInfo chainInfo);
//创建数据导出执行器DataExportExecutor, 配置导出配置
DataExportExecutor create(ExportDataSource dataSource, ChainInfo chainInfo, ExportConfig config)
//DataExportExecutor启动
start(DataExportExecutor exportExecutor)
//DataExportExecutor关闭
stop(DataExportExecutor exportExecutor)
```
<br />**参数ExportDataSource为数据源配置，参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| autoCreateTable | 是否自动建表 | boolean |false |
| sharding | 是否多数据源，采用分库分表 | boolean |false |
| shardingNumberPerDatasource | 单库表分片数，用于路由和建表 | int | 0 |
| mysqlDataSources | mysql数据源配置，支持多数据源 | List<MysqlDataSource> | null |
| esDataSource | es数据源配置 | ESDataSource | null |

<br />**数据源参数支持了mysql和es，包括MysqlDataSource, ESDataSource，参数如下：**

<br />**MysqlDataSource（必须），参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| jdbcUrl | jdbc连接配置，格式：jdbc:mysql://[ip]:[port]/[database] | string | null |
| user | 用户名 | string | null |
| pass | 密码 | string | null |

<br />**ESDataSource（非必须），参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| enable | es存储开关 | boolean | false |
| clusterName | 集群名称 | string | null |
| ip | IP地址 | string | null |
| port | 端口号 | int | null |


<br />**ChainInfo为链参数配置（必须），参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| nodeStr | 节点ip和端口port，格式为：[ip]:[port] | string | null |
| groupId | 分组id | int | null |
| certPath | 链节点连接所需证书路径 | string | null |


<br />**ExportConfig为数据导出任务配置（非必须），参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| crawlBatchUnit | 链上导出任务每批次数目 | int | 1000 |
| frequency | 任务间隔时间 | int | 5s |
| startBlockHeight | 开始区块高度 | int | 0 |
| startDate | 开始时间 | Date | null |
| ContractInfoList | 合约信息，包括合约名称，abi，binary信息（基础数据无需填充）| List<ContractInfo> | null |
| dataTypeBlackList | 导出数据表黑名单，默认全部导出，可根据DataType枚举设置 | List<DataType> | null |
| multiLiving | 是否开启多活job | boolean |false |
| zookeeperServiceLists | zk服务节点列表(,分隔),格式：[IP]:[port],[IP]:[port] | string | null |
| zookeeperNamespace | zk命名空间(,分隔) | string | null |
| prepareTaskJobCron | 任务准备job定时配置 | string | "0/"+ frequency + " * * * * ?" |
| dataFlowJobCron | 任务分片执行job定时配置 | string |"0/"+ frequency + " * * * * ?" |
| dataFlowJobItemParameters | 任务分片执行job参数 | string | 如 "0=A,1=B,2=C,3=D,4=E,5=F,6=G,7=H" |
| dataFlowJobShardingTotalCount | 任务分片数目 | int | 8 |
