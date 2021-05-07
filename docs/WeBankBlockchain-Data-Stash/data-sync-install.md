### 数据同步

请在要同步数据的节点上执行以下步骤。

#### 获取启动脚本和配置文件

```
curl -#LO https://github.com/WeBankBlockchain/Data-Link/releases/download/V1.0.0/data-sync-bash.tar.gz
```

解压文件包至当前目录
```
tar -zxvf data-sync-bash.tar.gz && cd data-sync && chmod -x data_sync.sh
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

[data]
sync.dataPath=./data
sync.type=RocksDB
```

同步后存储支持RocksDB和Scalable两种模式，可按需配置。

#### 启动脚本

```
./data_sync.sh
```

上述脚本会自动拉取对应系统的data-sync包，进行执行。

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

同步后的数据可在配置的dataPath路径下看到，如下：

```
ls data/
000006.log      000007.sst      CURRENT         IDENTITY        LOCK            LOG             MANIFEST-000008 OPTIONS-000005
```