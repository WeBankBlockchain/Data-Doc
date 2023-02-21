### SDK方式调用

#### 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明                                                                                                                                                        |备注|
| --- |-----------------------------------------------------------------------------------------------------------------------------------------------------------| --- |
| FISCO-BCOS | 支持2.x版本， 1.x版本请参考V0.5版本, 3.x版本请参考[3.x版本SDK调用](https://data-doc.readthedocs.io/zh_CN/data_export_3.0/docs/WeBankBlockchain-Data-Export/install_SDK_2.html) |
| Java | JDK[1.8]                                                                                                                                                  | |
| MySQL | >= mysql-community-server[5.7]                                                                                                                            | |
| ElasticSearch | >= elasticsearch [7.0]                                                                                                                                    | 只有在需要ES存储时安装 |
| zookeeper | >= zookeeper[3.4]                                                                                                                                         | 只有在进行集群部署的时候需要安装|


#### 部署步骤

##### 引入数据导出SDK依赖 

###### 建立依赖
```
dependencies {
    compile 'com.webank:data-export-sdk:1.7.9'
}
```

#### SDK调用

##### 单库基础数据导出使用例子如下（默认导出配置）：
```
//数据库配置信息
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        .build();
//mysql数据库列表
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
//mysql数据库添加
mysqlDataSourceList.add(mysqlDataSourc);
//导出数据源配置
ExportDataSource dataSource = ExportDataSource.builder()
        //设置mysql源
        .mysqlDataSources(mysqlDataSourceList)
        //自动建表开启
        .autoCreateTable(true) 
        .build();
//channel通道的数据导出执行器构建
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, ChainInfo.builder()
        //链节点连接信息
        .nodeStr("[ip]:[port]")
        //链连接证书位置
        .certPath("config")
        //群组id
        .groupId(1)
        .build());
//数据导出执行启动
ExportDataSDK.start(exportExecutor);
//休眠一定时间，因导出执行为线程池执行，测试时主线程需阻塞
Thread.sleep(60 *1000L);
//数据导出执行关闭
//ExportDataSDK.stop(exportExecutor);
```


##### 分库分表基础数据导出使用例子如下（默认导出配置）：
```
//数据库配置信息
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        .build();
//数据库配置信息
MysqlDataSource mysqlDataSourc1 = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        .build();
//mysql数据库列表
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
mysqlDataSourceList.add(mysqlDataSourc);
mysqlDataSourceList.add(mysqlDataSourc1);
//导出数据源配置
ExportDataSource dataSource = ExportDataSource.builder()
        //设置mysql源
        .mysqlDataSources(mysqlDataSourceList)
        //自动建表开启
        .autoCreateTable(true)
        //分库分表开启
        .sharding(true)
        //分片数设置
        .shardingNumberPerDatasource(2)
        .build();
//channel通道的数据导出执行器构建
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, ChainInfo.builder()
        //链节点连接信息
        .nodeStr("[ip]:[port]")
        //链连接证书位置
        .certPath("config")
        //群组id
        .groupId(1)
        .build());
//数据导出执行启动
ExportDataSDK.start(exportExecutor);
//休眠一定时间，因导出执行为线程池执行，测试时主线程需阻塞
Thread.sleep(60 *1000L);
//数据导出执行关闭
//ExportDataSDK.stop(exportExecutor);
```

##### JSON-RPC方式导出（默认导出配置）：

SDK也支持通过链JSON RPC方式导出数据，如下

```
//数据库配置信息
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        .build();
//mysql数据库列表
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
//mysql数据库添加
mysqlDataSourceList.add(mysqlDataSourc);
//导出数据源配置
ExportDataSource dataSource = ExportDataSource.builder()
        //设置mysql源
        .mysqlDataSources(mysqlDataSourceList)
        //自动建表开启
        .autoCreateTable(true) 
        .build();
//RPC通道类型的数据导出执行器构建
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, ChainInfo.builder()
        .rpcUrl("http://127.0.0.1:8546")
        // chain cryptoType, gm-1
        .cryptoTypeConfig(0)
        .groupId(1)
        .build());
//数据导出执行启动
ExportDataSDK.start(exportExecutor);
//休眠一定时间，因导出执行为线程池执行，测试时主线程需阻塞
Thread.sleep(60 *1000L);
//数据导出执行关闭
//ExportDataSDK.stop(exportExecutor);
```

##### Data-Stash仓库源方式导出（默认导出配置）：

SDK打通了[Data-Stash组件](https://data-doc.readthedocs.io/zh_CN/latest/docs/WeBankBlockchain-Data-Stash/intro.html) ，支持从Data-Stash仓库源中导出数据，如下

```
//数据库配置信息
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        .build();
//mysql数据库列表
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
//mysql数据库添加
mysqlDataSourceList.add(mysqlDataSourc);
//导出数据源配置
ExportDataSource dataSource = ExportDataSource.builder()
        //设置mysql源
        .mysqlDataSources(mysqlDataSourceList)
        //自动建表开启
        .autoCreateTable(true) 
        .build();
//stash仓库源类型的数据导出执行器构建
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, StashInfo.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]?autoReconnect=true&serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8")
        .user("username")
        .pass("password")
        // chain cryptoType, gm-1
        .cryptoTypeConfig(0)
        .build());
//数据导出执行启动
ExportDataSDK.start(exportExecutor);
//休眠一定时间，因导出执行为线程池执行，测试时主线程需阻塞
Thread.sleep(60 *1000L);
//数据导出执行关闭
//ExportDataSDK.stop(exportExecutor);
```


<br />**更多使用方式参照[Sdk Java API](sdk_spi.md)**

#### SDK-Demo

项目提供使用demo，详情见[Data-Export-SDK-Demo](https://github.com/WeBankBlockchain/Data-Export-Demo)

```eval_rst
.. note::
    - 如果因为网络问题导致长时间无法访问，请访问：https://gitee.com/WeBankBlockchain/Data-Export-Demo
```
