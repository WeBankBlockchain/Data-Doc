# 详细配置
## 数据仓库
数据仓库组件的完整配置选项如下：

| 配置项 | 说明 | 是否必需 | 说明|
| --- | --- | --- | --- |
|system.binlogAddress|nginx服务的binlog地址，如果连接多个节点的话，使用逗号分隔|必选|示例：system.binlogAddress=http://www.example.com:5299/, http://www.example.com:5300/|
|spring.datasource.url|数据库连接URL。请确保数据库已预先建立。|是|示例：jdbc:mysql://127.0.0.1:3306/stash?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8|
|spring.datasource.username|用户名|是|示例：root|
|spring.datasource.password|密码|是|示例：123456|
|spring.datasource.driverClassName | JDBC驱动|是 |示例：com.mysql.cj.jdbc.Driver|
|system.encryptType|0-非国密，1-国密。binlog中签名验证时需要使用与链一致的哈希。|否|默认非国密|
|system.localBinlogPath|binlog的下载位置，支持相对地址和绝对地址|否|示例：~/binlogs|
|system.batchCount|数据库批量插入条数|否|默认为50|
|system.ledgerThreads|账本表插入线程池大小|否|默认为10|
|system.ledgerQueueSize|账本表插入队列大小|否|默认为10|
|system.stateThreads|状态表插入线程池大小|否|默认为10|
|system.stateQueueSize|状态表插入队列大小|否|默认10|
|system.binlogVerify|1-开启binlog校验，0-不开启|否|默认关闭|
|read.blocks|每下载多少文件开始执行入库|否|默认为5|
|read.clean|yes-入库完成后清理文件。no-不清理|否|默认清理|

## 数据同步

数据同步模块完整配置选项如下：

| 配置项 | 说明 | 是否必需 | 默认|
| --- | --- | --- | --- |
|stash.ip|数据仓库数据库IP地址|必选|127.0.0.1|
|stash.port|数据仓库数据库端口|必选|3306|
|stash.dbname|数据仓库数据库名|必选|stash|
|stash.username|数据仓库数据库用户名|必选|root|
|stash.password|数据仓库数据库密码|必选|123456|
|node.groupId|待同步群组id|必选|1|
|node.path|节点路径|必选|默认当前路径|
|sync.endBlockNumber|指定同步截止区块号，后续区块从其他节点拉取|非必选|默认：10000|
|sync.pageCount|指定除表_sys_hash_2_block_和表_sys_block_2_nonces_之外其他表的分页拉取行数，默认为1000行每页；|非必选|1000|
|sync.bigTablePageCount|指定表_sys_hash_2_block_和表_sys_block_2_nonces_的分页拉取行数，默认为1000行每页|非必选|1000|