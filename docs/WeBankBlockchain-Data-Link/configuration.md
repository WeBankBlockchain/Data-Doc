# 详细配置

Data-Link完整配置选项如下：

| 配置项 | 说明 | 是否必需 | 默认|
| --- | --- | --- | --- |
|stash.ip|数据仓库数据库IP地址|必选|127.0.0.1|
|stash.port|数据仓库数据库端口|必选|3306|
|stash.dbname|数据仓库数据库名|必选|stash|
|stash.username|数据仓库数据库用户名|必选|root|
|stash.password|数据仓库数据库密码|必选|123456|
|sync.dataPath|数据写入节点存储地址|非必选|默认：./data|
|sync.type|同步类型：RocksDB，Scalable|非必选|默认：RocksDB|
|sync.endBlockNumber|指定同步截止区块号|非必选|默认：10000|
|sync.pageCount|指定除表_sys_hash_2_block_和表_sys_block_2_nonces_之外其他表的分页拉取行数，默认为1000行每页；|非必选|1000|
|sync.bigTablePageCount|指定表_sys_hash_2_block_和表_sys_block_2_nonces_的分页拉取行数，默认为1000行每页|非必选|1000|

