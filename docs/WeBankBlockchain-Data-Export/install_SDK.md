## SDK-快速开始

### 1. 前置依赖

在使用本组件前，请确认系统环境已安装相关依赖软件，清单如下：

| 依赖软件 | 说明 |备注|
| --- | --- | --- |
| FISCO-BCOS | >= 2.0， 1.x版本请参考V0.5版本 |
| Java | JDK[1.8] | |
| Git | 下载的安装包使用Git | |
| MySQL | >= mysql-community-server[5.7] | |
| ElasticSearch | >= elasticsearch [7.0] | 只有在需要ES存储时安装 |
| zookeeper | >= zookeeper[3.4] | 只有在进行集群部署的时候需要安装|


### 2. 部署步骤

#### 引入数据导出SDK依赖 

##### 在java项目的build.gradle中引入如下Maven库
```
allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```
##### 建立依赖
```
dependencies {
	implementation 'com.github.WeBankBlockchain:Data-Export:DataExport-SDK-Beta-SNAPSHOT'
}
```


### 3. SDK调用

#### 单库基础数据导出使用例子如下（默认导出配置）：
```
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]")
        .pass("password")
        .user("username")
        .build();
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
mysqlDataSourceList.add(mysqlDataSourc);
ExportDataSource dataSource = ExportDataSource.builder()
        .mysqlDataSources(mysqlDataSourceList)
        .autoCreateTable(true)
        .build();
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, ChainInfo.builder()
         .nodeStr("[ip]:[port]")
        .certPath("config")
        .groupId(1).build());
ExportDataSDK.start(exportExecutor);
//Thread.sleep(60 *1000L);
//ExportDataSDK.stop(exportExecutor);
```


#### 分库分表基础数据导出使用例子如下（默认导出配置）：
```
MysqlDataSource mysqlDataSourc = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]")
        .pass("password")
        .user("username")
        .build();
MysqlDataSource mysqlDataSourc1 = MysqlDataSource.builder()
        .jdbcUrl("jdbc:mysql://[ip]:[port]/[database]")
        .pass("password")
        .user("username")
        .build();
List<MysqlDataSource> mysqlDataSourceList = new ArrayList<>();
mysqlDataSourceList.add(mysqlDataSourc);
mysqlDataSourceList.add(mysqlDataSourc1);
ExportDataSource dataSource = ExportDataSource.builder()
        .mysqlDataSources(mysqlDataSourceList)
        .autoCreateTable(true)
        .sharding(true)
        .shardingNumberPerDatasource(2)
        .build();
DataExportExecutor exportExecutor = ExportDataSDK.create(dataSource, ChainInfo.builder()
        .nodeStr("[ip]:[port]")
        .certPath("config")
        .groupId(1).build());
ExportDataSDK.start(exportExecutor);
//Thread.sleep(60 *1000L);
//ExportDataSDK.stop(exportExecutor);
```

