# 数据仓库组件 

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)

```eval_rst
.. admonition:: **简介**

    Data-Stash是基于FISCO-BCOS的数据仓库组件，通过解析节点的binlog日志，生成该节点的全量备份，从而使节点能够实现冷热数据分离、快速同步成为可能。除了生成全量备份外，还支持binlog校验、断点续传等功能。
```

```eval_rst
.. image:: ../../images/overview/Data-Stash.png
```

```eval_rst
.. admonition:: **主要特性**

    - 节点账本全量备份
    - 多维度binlog校验
    - 备份数据的可信存储
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
   advance.md
   design.md
   faq.md
   appendix.md
```
