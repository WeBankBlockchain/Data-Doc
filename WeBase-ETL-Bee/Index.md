
FISCO BCOS允许各节点将状态变更记录到binlog日志中。WeBASE-ETL-Bee是基于FISCO-BCOS的全量数据服务，通过解析节点的binlog日志，生成该节点状态的全量备份，从而使节点能够实现冷热数据分离和数据裁剪。目前支持FISCO BCOS 2.6+。

全量数据服务的特性：

节点binlog日志的下载、解析、校验
节点状态的全量备份
节点状态的可信存储

索引：