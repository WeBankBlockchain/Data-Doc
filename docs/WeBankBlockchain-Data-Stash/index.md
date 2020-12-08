# 数据仓库组件 

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)

```eval_rst
.. admonition:: **简介**

    Data-Stash是基于FISCO-BCOS的数据仓库组件，通过解析节点的binlog日志，生成该节点状态的全量备份，从而使节点能够实现冷热数据分离和数据裁剪。FISCO BCOS允许各节点将状态变更记录到binlog日志中以及从binlog文件中恢复数据。
```

```eval_rst
.. image:: ../../images/overview/Data-Stash.png
```

```eval_rst
.. admonition:: **主要特性**

    - 提供binlog日志的下载、解析
    - 多重binlog校验手段
    - 节点状态的全量备份
    - 节点状态的可信存储
    - 支持断点续传
    - 轻量级接入
    - ...
```
```eval_rst
.. toctree::
   :maxdepth: 3
   
   intro.md
   quickstart.md
   configuration.md
   design.md
   faq.md
   appendix.md
```
