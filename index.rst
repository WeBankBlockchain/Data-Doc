##############################################################
WeBankBlockchain-Data 技术文档
##############################################################

.. admonition:: 什么是 WeBankBlockchain-Data 

    WeBankBlockchain-Data 是一套稳定、高效、安全的区块链数据治理组件解决方案，可无缝适配FISCO BCOS区块链底层平台。
    它由数据导出组件(Data-Export)、数据仓库组件(Data-Stash)、数据对账组件(Data-Reconcile)这三款相互独立、可插拔、可灵活组装的组件所组成，开箱即用，灵活便捷，易于二次开发。

    这三个组件分别从底层数据存储层、智能合约数据解析层和应用层三个方面，提供了区块链数据挖掘、裁剪、扩容、可信存储、抽取、分析、审计、对账、监管等数据治理方面的关键能力。
    WeBankBlockchain-Data已在金融、公益、农牧产品溯源、司法存证、零售等多个行业落地和使用。

.. admonition:: 设计目标

    在区块链底层和区块链生产应用之间，横亘着一条区块链技术、业务和产品的鸿沟。

    区块链数据治理的成本较高。首先，区块链节点的数据一般以Key-Value的形式存储于文件数据库，通常只能通过智能合约的接口来获取和调用，较难抽取、分析和处理。
    同时，区块链节点的数据还存在着扩容瓶颈，冷热数据切分困难。最后，区块链链上的数据需要经过多方共识，链上计算和处理的开销巨大。
    而区块链生产应用的开发者从了解区块链到完成开发，需要经历陡峭的学习曲线，花费较多的时间和精力。

    WeBankBlockchain-Data 定位为区块链数据治理组件，旨在通过关注区块链数据的计算和存储的不变，抓住数据治理的本质，使得区块链生产应用的开发者即便在不了解区块链的细节的场景下，也可以轻松、顺畅地管理、使用区块链数据，提供开箱即用和一站式的友好体验。


.. admonition:: 组件简介

    - **WeBankBlockchain-Data-Stash  数据仓库组件** 
    提供FISCO BCOS节点数据扩容、备份和裁剪的能力。
    可基于binlog协议同步区块链底层节点数据，支持断点续传，数据可信验证，并提供快速同步机制。
    请参考  `Github地址 <https://github.com/WeBankBlockchain/Data-Stash>`_ 
    `Gitee地址 <https://github.com/FISCO-BCOS/FISCO-BCOS>`_
    `文档 <https://gitee.com/WeBankBlockchain/Data-Stash>`_ 

    - **WeBankBlockchain-Data-Export  数据导出组件** 
    支持将链上数据导出到MySQL等结构化存储中，解决区块链数据复杂查询、分析和处理的问题。
    只需简单配置、无需开发、即可实时导出个性化的业务数据，实现将裸数据转化为标准化、结构化、有序化、可视化的高价值数据。
    请参考  `Github地址 <https://github.com/WeBankBlockchain/Data-Export>`_ 
    `Gitee地址 <https://github.com/FISCO-BCOS/FISCO-BCOS>`_
    `文档 <https://gitee.com/WeBankBlockchain/Data-Export>`_
    
    - **WeBankBlockchain-Data-Reconcile  数据对账组件**
    提供区块链数据的对账解决方案。
    灵活配置、无需开发，支持自定义对账数据和对账格式，支持定时对账和触发对账，对账处理模块可插拔可扩展。
    请参考  `Github地址 <https://github.com/WeBankBlockchain/Data-Reconcile>`_ 
    `Gitee地址 <https://github.com/FISCO-BCOS/FISCO-BCOS>`_
    `文档 <https://gitee.com/WeBankBlockchain/Data-Reconcile>`_


.. admonition:: 总体设计

    下图是数据治理组件使用的全景图。

.. image:: images/overview/data-comp-design.png


.. toctree::
   :maxdepth: 3

   ./docs/WeBankBlockchain-Data-Stash/index.md
   ./docs/WeBankBlockchain-Data-Export/index.md
   ./docs/WeBankBlockchain-Data-Reconcile/index.md
.. 


 
 
