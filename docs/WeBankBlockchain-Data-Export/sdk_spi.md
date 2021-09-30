### SDK Java API

#### 接口说明
```
//创建数据导出执行器DataExportExecutor，导出配置采用默认配置，从链上导出数据
DataExportExecutor create(ExportDataSource dataSource, ChainInfo chainInfo);

//创建数据导出执行器DataExportExecutor, 配置导出配置，从链上导出数据
DataExportExecutor create(ExportDataSource dataSource, ChainInfo chainInfo, ExportConfig config)

//创建Stash数据导出执行器DataExportExecutor，导出配置采用默认配置，从数据仓库中导出数据
DataExportExecutor create(ExportDataSource dataSource, StashInfo stashInfo);

//创建Stash数据导出执行器DataExportExecutor, 配置导出配置，从数据仓库中导出数据
DataExportExecutor create(ExportDataSource dataSource, StashInfo stashInfo, ExportConfig config)

//DataExportExecutor启动
start(DataExportExecutor exportExecutor)

//DataExportExecutor关闭
stop(DataExportExecutor exportExecutor)
```

#### 参数说明

**参数ExportDataSource为数据源配置，参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| autoCreateTable | 是否自动建表 | boolean |false |
| sharding | 是否多数据源，采用分库分表 | boolean |false |
| shardingNumberPerDatasource | 单库表分片数，用于路由和建表 | int | 0 |
| mysqlDataSources | mysql数据源配置，支持多数据源 | List<MysqlDataSource> | null |
| esDataSource | es数据源配置 | ESDataSource | null |

<br />**数据源参数支持了mysql和es，包括MysqlDataSource, ESDataSource，参数如下：**

**MysqlDataSource（必须），参数如下：**

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


<br />**ChainInfo为链参数配置，支持RPC通道和Channel通道两种配置方式，参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| groupId | 分组id (必配)| int | null |
| nodeStr | 链节点ip和端口port，格式：[ip]:[port] (channel通道连接时设置) | string | null |
| certPath | 链节点连接所需证书路径 (channel通道连接时设置)| string | null |
| rpcUrl | rpc连接url，格式：http://[ip]:[port]，如：http://127.0.0.1:8546；（使用rpc连接时设置）| string | null |
| cryptoTypeConfig | 链密钥类型，0-ECDSA，1-SM（国密）,（使用rpc连接时设置） | int | 0 |

```eval_rst
.. note::
    - 链上证书默认位于链节点 ~/fisco/nodes/127.0.0.1/sdk目录中，如果为国密链直接将sdk下gm文件夹拷贝到certPath配置的目录中。
    - channel通道和rpc通道同时设置时，将使用rpc方式连接。

```

<br />**StashInfo为数据仓库源mysql配置，与ChainInfo同时设置后，优先该方式，参数如下：**

| 参数 | 说明 | 类型 | 默认值 |
| --- | --- | --- | ---|
| jdbcUrl | 数据仓库jdbc连接配置，格式：jdbc:mysql://[ip]:[port]/[database] | string | null |
| user | 用户名 | string | null |
| pass | 密码 | string | null |
| cryptoTypeConfig | 链密钥类型，0-ECDSA，1-SM（国密） | int | 0 |


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
| generatedOff | 合约事件或函数导出过滤,如: Map<合约名, 方法名/事件名>| Map | empty map |
| ignoreParam | 合约函数指定字段导出过滤,如: Map<合约名, Map<方法名/事件名, List<字段名>>> | Map | empty map |
| paramSQLType | 合约函数指定字段导出sql类型,如: Map<合约名, Map<方法名/事件名, Map<字段名,sql类型>>> | Map| empty map |
| tablePrefix | 数据导出表名前缀设置 | String | 空 |
| tablePostfix | 数据导出表名前缀设置 | String | 空 |
| namePrefix | 合约导出表字段前缀设置，只针对method、event表中变量字段 | String | 空 |
| namePostfix | 合约导出表字段后缀设置，只针对method、event表中变量字段 | String | 空 |
| ignoreBasicDataTableParam | 原始数据表指定字段导出过滤,如: Map<表名, List<字段名>>| Map | empty map |
| topicRegistry | 自定义处理逻辑，支持区块、交易、交易回执、函数、事件| SubscriberInterface | empty  |

ignoreBasicDataTableParam 参考例子如下，支持过滤表和字段参考 IgnoreBasicDataParam 枚举类：
```
    ExportConfig config = new ExportConfig();
    List<String> params = new ArrayList<>();
    params.add(IgnoreBasicDataParam.BlockRawDataParams.EXTRA_DATA.name());
    params.add(IgnoreBasicDataParam.BlockRawDataParams.LOGS_BLOOM.name());
    params.add(IgnoreBasicDataParam.BlockRawDataParams.RECEIPTS_ROOT.name());
    params.add(IgnoreBasicDataParam.BlockRawDataParams.TRANSACTION_LIST.name());
    params.add(IgnoreBasicDataParam.BlockRawDataParams.GAS_USED.name());
    //添加block_raw_data表字段过滤配置
    config.getIgnoreBasicDataTableParam()
          .put(IgnoreBasicDataParam.IgnoreBasicDataTable.BLOCK_RAW_DATA_TABLE.name(), params);
```
自定义事件处理逻辑示例
```
    ExportConfig config = new ExportConfig();
    //添加区块自定义处理逻辑
    exportConfig.getTopicRegistry().getBlockTopic().addSubscriber(new SubscriberInterface<BlockInfoBO>() {
        @Override
        public boolean shouldProcess(BlockInfoBO blockInfoBO, Object context) {
            return blockInfoBO.getBlockDetailInfo().getBlockHeight() >= 10;
        }

        @Override
        public void process(BlockInfoBO blockInfoBO) {
            System.out.println(blockInfoBO.getBlockDetailInfo().getBlockHash());
        }
    });
```


#### 功能说明

- 本版本为SDK版本，去Spring等框架依赖，更好地支持第三方以jar形式引入
- 支持分库分表和分布式调度
- 增加导出启停的API接口
- 支持导出数据类型可选功能
- 支持多链多群组调用
- 支持数据库和ES存储
- 暂不支持指定合约分表数目配置功能，如需使用可使用服务方式配置启动
