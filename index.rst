##############################################################
WeBankBlockchain-Data 技术文档
##############################################################

## 什么是 WeBankBlockchain-Data ？

    WeBankBlockchain-Data 是一套基于开源的稳定、高效、安全的区块链底层平台FISCO BCOS数据治理组件解决方案组成的生态圈。
    它由数据导出组件、全量数据服务、数据对账组件这3款相互独立，而又能可插拔灵活组装的组件所组成。

    这三个组件分别从底层数据存储层、智能合约数据解析层和应用层三个方面，提供了区块链数据挖掘、裁剪、扩容、可信存储、抽取、分析、审计、对账、监管等数据治理方面的关键能力。

## 设计目标

    在区块链底层和区块链生产应用之间，横亘着一条区块链技术、业务和产品的鸿沟。

    区块链节点的数据一般以Key-Value的形式存储于文件数据库，通常只能通过智能合约的接口来获取和调用，且链上存储的数据需要经过共识节点之间的多方共识，交叉验证。
    而区块链生产应用的开发者从了解区块链到完成开发，需要经历陡峭的学习曲线，花费较多的时间和精力。

    WeBankBlockchain-Data 定位为区块链数据治理组件，旨在通过关注区块链数据的计算和存储的不变，抓住数据治理的本质，使得区块链生产应用的开发者即便在不了解区块链的细节的场景下，也可以轻松、顺畅地管理、使用区块链数据，提供开箱即用和一站式的友好体验。


## 组件简介

    - **Data-Export 数据导出组件** 
    支持将链上数据导出到MySQL等结构化存储中，解决区块链数据复杂查询、分析和处理的问题。
    只需简单配置、无需开发、即可实时个性化的导出业务数据，并提供REST API和可视化能力。请参考 `文档 <./docs/WeBankBlockchain-Data-Export/index.html>`_
    
    - **Data-Stash 全量数据服务** 
    提供FISCO BCOS节点数据扩容、备份和裁剪的能力。
    基于binlog协议同步，支持断点续传，数据可信验证，并提供快速同步机制。请参考 `文档 <./docs/WeBase-ETL-Bee/index.html>`_ 
    
    - **Data-Reconcile 数据对账组件**
    提供区块链数据的对账解决方案。
    灵活配置、无需开发，支持自定义对账数据和对账格式，支持定时对账和触发对账，对账处理模块可插拔可扩展。请参考 `文档 <./docs/WeBankBlockchain-Bc-Reconcile/index.html>`_

## 总体设计

下图是各个组件互相配合的场景。

![](../images/overview/data-comp-design.png)


.. toctree::
   :maxdepth: 3

   ./docs/WeBankBlockchain-Data-Export/index.md
   ./docs/WeBase-ETL-Bee/index.md
   ./docs/WeBankBlockchain-Data-Reconcile/index.md
.. 


 
 
