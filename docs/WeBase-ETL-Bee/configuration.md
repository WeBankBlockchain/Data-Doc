# 详细配置

全量数据服务的完整配置选项如下：

| 配置项 | 说明 | 是否必需 | 说明|
| --- | --- | --- | --- |
|system.binlogAddress|nginx服务的binlog地址，如果连接多个节点的话，使用逗号分隔|必选|示例：system.binlogAddress=http://www.example.com:5299/, http://www.example.com:5300/|
|spring.datasource.url|数据库连接URL|是|示例：jdbc:mysql://127.0.0.1:3306/etl?autoReconnect=true&characterEncoding=utf8&useSSL=false&serverTimezone=GMT%2b8|
|spring.datasource.username|用户名|是|示例：root|
|spring.datasource.password|密码|是|示例：123456|
|spring.datasource.driverClassName | JDBC驱动|是 |示例：com.mysql.jdbc.Driver 或 com.mysql.cj.jdbc.Driver|
|system.encryptType|0-非国密，1-国密|否|默认非国密|
|system.localBinlogPath|binlog的下载位置，支持相对地址和绝对地址|否|示例：~/binlogs|
|system.batchCount|数据库批量插入条数|否|默认为5|
|system.binlogVerify|1-开启binlog校验，0-不开启|否|默认开启|
|system.checkPointVerify|1-开启检查点校验，0-不开启|否|默认开启|
|read.blocks|每下载多少文件开始执行入库|否|默认为5|
|read.clean|yes-入库完成后清理文件。no-不清理|否|默认清理|

