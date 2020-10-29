# 组件介绍

FISCO BCOS允许各节点将状态变更记录到binlog日志中。WeBASE-ETL-Bee是基于FISCO-BCOS的全量数据服务，通过解析节点的binlog日志，生成该节点状态的全量备份，从而使节点能够实现冷热数据分离和数据裁剪。目前支持FISCO BCOS 2.6+。

主要特性：

```eval_rst
   .. admonition:: **主要特性**

    - 提供binlog日志的下载、解析
    - 节点状态的全量备份
    - 节点状态的可信存储
    - 节点状态对比校验
    - 节点数据链式校验
    - 轻量级接入
```
```eval_rst
   .. toctree::
   
   :maxdepth: 4

   quickstart.md
   configuration.md
   appendix.md
   faq.md
```

