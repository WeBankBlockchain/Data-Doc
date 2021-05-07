### 数据裁剪查询

#### 使用步骤

##### 配置工程

###### 配置文件设置

修改application.properties文件中数据裁剪相关配置：

```
data.query.enable=true
data.query.certPath=./config
data.query.groupId=1
data.query.nodeStr=127.0.0.1:20200
data.query.topic=DB
```

###### 配置证书文件

连接链节点时，需配置证书或证书路径。

将链SDK证书拷贝到配置的证书路径下，如 **./config**下，SDK证书目录位于nodes/${ip}/sdk/目录下
```
# 假设SDK证书位于~/fisco/nodes/127.0.0.1/sdk/目录
cp -r ~/fisco/nodes/127.0.0.1/sdk/* ./config/
```

#### 运行程序

修改配置后，重新启动项目

```
bash stop.sh
bash start.sh
```

#### 节点数据裁剪

##### 节点配置

FISCO BCOS节点在配置scalable存储模式时，对于本地经裁剪后缺少的数据，将通过Data-Stash访问数据源进行获取。

###### 模式启用

- 设置群组的ini配置文件中`[storage].type=scalable`来选择链的存储模式为`scalable`。
- 设置群组的ini配置文件中`[storage].binary_log=true`来启用binlog。如用户使用build_chain脚本搭链，并选择scalable存储模式，配置文件会自动开启binlog。

###### 文件组织

节点使用scalable存储模式后，其数据目录内容如下：

- `nodeX/data/groupX/block/Scalable/blocksDB/`下分文件夹存储区块数据。每个文件夹为独立的DB实例，用DB记录的首个块高来进行文件夹命名。用户在配置了数据仓库实现数据备份归档后，较旧（较低块高）的子文件夹**允许删除**。
- `nodeX/data/groupX/block/Scalable/state`存储整体的状态数据，该文件夹**不可删除**。

###### binlog

binlog文件记录了每个区块的每个交易对区块链状态的修改结果。binlog机制的作用在于：

1. 提供了区块维度的数据操作结果的记录；
2. 节点可通过binlog文件而非通过原有的拉取区块重放交易的方式来恢复数据；
3. binlog文件为“数据仓库”的快照构建提供数据来源。

用户可通过设置群组的ini配置文件中`[storage].binary_log=true`来启用binlog（binlog默认关）。开启binlog后，`nodeX/data/groupX/BinaryLogs/`的目录如下。每个binlog文件以其记录的首个区块内容进行命名，且按从小到大的顺序来记录区块。如下图中，文件`18.binlog`记录的区块为块高18到块高29。

```bash
├── 0.binlog
├── 18.binlog
├── 30.binlog
└── 32.binlog
```

##### 节点启动

停止节点，在对节点数据进行上述裁剪后，启动节点