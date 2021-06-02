### 数据快速同步

请在要同步数据的节点上执行以下步骤，同步前新节点保持在未启动状态，并清空节点data目录。

#### 获取启动脚本和配置文件

```
curl -#LO https://github.com/WeBankBlockchain/Data-Stash/releases/download/V1.2.0/data-sync-bash.tar.gz
```

解压文件包至当前目录
```
tar -zxvf data-sync-bash.tar.gz && cd data-sync-bash && chmod -x data_sync.sh
```

data-sync目录如下：
```
├── data-sync
│   ├── config.conf
│   └── data_sync.sh
```

```eval_rst
.. note::
    - config.conf为配置文件，数据仓库数据源配置。
    - data_sync.sh为启动脚本
```


#### 配置文件

修改config.conf文件：该文件包含了所有的配置信息。以下配置信息是必须要配置的：

```
[stash]
stash.ip=127.0.0.1
stash.port=3306
stash.dbname=stash
stash.username=root
stash.password=123456

[node]
#要导出的群组ID，会根据配置读取节点目录conf/下指定group.id.ini配置，进行数据同步
node.groupId=1
#节点路径，若选择在fisco对应节点目录下(如~/fisco/nodes/127.0.0.1/node0)执行下述步骤，则无需配置节点路径，默认即可。
node.path=./

[more]
#同步截止区块号，如默认为10000，则同步0-9999号区块至新节点
sync.endBlockNumber=10000
```

更多配置参照[数据同步配置](./configuration.html#id3)

#### 启动脚本

```
bash data_sync.sh
```

```eval_rst
.. note::
         上述脚本会自动拉取对应系统的data-sync包，并自动读取节点群组的配置，同步数据到对应的节点存储源中，节点存储模式包括rocksdb/mysql/scalable三种。
         rocksdb/mysql模式为全量数据同步；scalable模式下，只同步状态数据，区块数据需要通过数据仓库获取。

```


看到如下日志，则表示执行成功：

```
... ...
[2021-04-29 15:08:24][1/34] processing _sys_tables_
[2021-04-29 15:08:24.287430] [0x00000001056c45c0] [trace]   [STORAGE]conversion end!
[2021-04-29 15:08:24.296431] [0x00000001056c45c0] [debug]   [STORAGE][RocksDB][Commit]Write to db,encodeTimeCost=1,writeDBTimeCost=8,totalTimeCost=9
[2021-04-29 15:08:24][1/34] _sys_tables_ downloaded items : 34 done.
[2021-04-29 15:08:24][2/34] processing _sys_hash_2_block_
[2021-04-29 15:08:24.322570] [0x00000001056c45c0] [trace]   [STORAGE]conversion table data,table name=_sys_hash_2_block_,new entry count=50,dirty entry count=0
[2021-04-29 15:08:24.325145] [0x00000001056c45c0] [trace]   [STORAGE]conversion end!
[2021-04-29 15:08:24.330759] [0x00000001056c45c0] [debug]   [STORAGE][RocksDB][Commit]Write to db,encodeTimeCost=4,writeDBTimeCost=1,totalTimeCost=5
... ...
```

如节点群组1的配置为RocksDB模式，则同步后的数据可在节点data/group1/block/RocksDB/路径下看到，如下文件：

```
ls data/
000006.log      000007.sst      CURRENT         IDENTITY        LOCK            LOG             MANIFEST-000008 OPTIONS-000005
```