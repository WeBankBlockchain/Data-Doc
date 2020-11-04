##############################################################
WeBankBlockchain-Data 技术文档
##############################################################


.. admonition:: 组件简介

    - **Data-Export 数据导出组件** 
    支持将链上数据导出到MySQL等结构化存储中，解决区块链数据复杂查询、分析和处理的问题。只需简单配置、无需开发、即可实时个性化的导出业务数据，并提供REST API和可视化能力。请参考 `文档 <./docs/WeBankBlockchain-Data-Export/index.html>`_
    - **Data-Stash 全量数据服务** 
    提供FISCO BCOS节点数据扩容、备份和裁剪的能力。基于binlog协议同步，支持断点续传，数据可信验证，并提供快速同步机制。请参考 `文档 <./docs/WeBase-ETL-Bee/index.html>`_ 
    - **Data-Reconcile 数据对账组件**
    提供区块链数据的对账解决方案。灵活配置、无需开发，支持自定义对账数据和对账格式，支持定时对账和触发对账，对账处理模块可插拔可扩展。请参考 `文档 <./docs/WeBankBlockchain-Bc-Reconcile/index.html>`_

.. toctree::
   :maxdepth: 3

   ./docs/WeBankBlockchain-Data-Export/index.md
   ./docs/WeBase-ETL-Bee/index.md
   ./docs/WeBankBlockchain-Data-Reconcile/index.md
.. 


 
 
