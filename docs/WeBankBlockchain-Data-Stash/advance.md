# 高级配置

## 分库分表配置
随着链上数据不断增大，对应的全量数据仓库的存储压力也增大。当数据量大到一定程度的时候，单库的存储、内存等资源已不足以支撑相关业务的需求，因此需要进行分库分表。通常，当单库压力达到1TB，单表数据达10G的时候，推荐进行分库分表。
### 分库建议
建议优先对账本表进行分库分表，通常账本表占据了系统存储容量的99%以上，可以通过类似下述sql查询各表数据量：
```
select table_schema, table_name, concat(truncate(data_length/1024/1024,2),' mb') as data_size,
concat(truncate(index_length/1024/1024,2),' mb') as index_size
from information_schema.tables where table_schema ='stash'
order by data_length desc;
```

通常，根据表的数据量，推荐按照下述策略进行分库分表。

| 表类型 | 功能 | 分片建议 | 说明 |
| --- | --- | --- | --- |
|账本表|区块信息|建议分片|_sys_hash_2_block_, _sys_tx_hash_2_block_，_sys_block_2_nonces_，_sys_hash_2_header_|
|账本表detail|区块变更历史|建议分片|_sys_hash_2_block_d_, _sys_tx_hash_2_block_d_,_sys_block_2_nonces_d_，_sys_hash_2_header_d_|
|状态表|区块信息|不用分库分表|_sys_current_state_, _sys_cns_，c_xxx等，数据量很小|
|状态表detail|状态变更历史|通常不用，按实际情况来定|_sys_current_state_d_, _sys_cns_d_,c_xxx等，数据量不大|
|进度控制表|控制进度|不用分片，或者广播表|block_task_pool等，数据量很小|

### 配置示例
#### 分库不分表
在application.properties中，注释原先spring.datasource相关配置，然后添加下述配置：

```
### 配置nginx服务的binlog地址，如果连接多个节点的话，使用逗号分隔
system.binlogAddress=http://127.0.0.1:5299/

### 分库分表配置
#### 打开开关
spring.shardingsphere.enabled=true
#### 定义数据源
spring.shardingsphere.datasource.names = stash1,stash2
spring.shardingsphere.datasource.stash1.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.stash1.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.stash1.jdbcUrl=jdbc:mysql://localhost:3306/stash1?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8
spring.shardingsphere.datasource.stash1.username=root
spring.shardingsphere.datasource.stash1.password=123456
spring.shardingsphere.datasource.stash1.hikari.maximum-pool-size=100

spring.shardingsphere.datasource.stash2.type=com.zaxxer.hikari.HikariDataSource
spring.shardingsphere.datasource.stash2.driver-class-name=com.mysql.cj.jdbc.Driver
spring.shardingsphere.datasource.stash2.jdbcUrl=jdbc:mysql://localhost:3306/stash2?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8
spring.shardingsphere.datasource.stash2.username=root
spring.shardingsphere.datasource.stash2.password=123456
spring.shardingsphere.datasource.stash2.maximum-pool-size=100

#### 不做分片的表，默认存储到stash1
spring.shardingsphere.sharding.default-data-source-name=stash1

#### 需分片的表，路由目标
spring.shardingsphere.sharding.tables._sys_hash_2_block_.actual‐data‐nodes = stash$->{1..2}._sys_hash_2_block_$->{1..3}
spring.shardingsphere.sharding.tables._sys_hash_2_block_d_.actual‐data‐nodes = stash$->{1..2}._sys_hash_2_block_d_$->{1..3}
spring.shardingsphere.sharding.tables._sys_tx_hash_2_block_.actual‐data‐nodes = stash$->{1..2}._sys_tx_hash_2_block_$->{1..3}
spring.shardingsphere.sharding.tables._sys_tx_hash_2_block_d_.actual‐data‐nodes = stash$->{1..2}._sys_tx_hash_2_block_d_$->{1..3}
spring.shardingsphere.sharding.tables._sys_block_2_nonces_.actual‐data‐nodes = stash$->{1..2}._sys_block_2_nonces_$->{1..3}
spring.shardingsphere.sharding.tables._sys_block_2_nonces_d_.actual‐data‐nodes = stash$->{1..2}._sys_block_2_nonces_d_$->{1..3}
spring.shardingsphere.sharding.tables._sys_hash_2_header_.actual‐data‐nodes = stash$->{1..2}._sys_hash_2_header_$->{1..3}
spring.shardingsphere.sharding.tables._sys_hash_2_header_d_.actual‐data‐nodes = stash$->{1..2}._sys_hash_2_header_d_$->{1..3}

#### 需分片的表，分片策略
spring.shardingsphere.sharding.default-database-strategy.standard.sharding-column= _num_
spring.shardingsphere.sharding.default-database-strategy.standard.precise-algorithm-class-name=com.webank.blockchain.data.stash.db.sharding.BlockShardingAlgorithm
spring.shardingsphere.sharding.default-database-strategy.standard.range-algorithm-class-name=com.webank.blockchain.data.stash.db.sharding.BlockShardingAlgorithm
spring.shardingsphere.sharding.default-table-strategy.standard.sharding-column= _num_
spring.shardingsphere.sharding.default-table-strategy.standard.precise-algorithm-class-name=com.webank.blockchain.data.stash.db.sharding.BlockShardingAlgorithm
spring.shardingsphere.sharding.default-table-strategy.standard.range-algorithm-class-name=com.webank.blockchain.data.stash.db.sharding.BlockShardingAlgorithm
#spring.shardingsphere.props.sql.show = false


### logback配置
logging.file=./logs/stash.log
logging.level.com.webank=INFO
```