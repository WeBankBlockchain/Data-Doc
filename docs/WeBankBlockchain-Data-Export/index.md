# 数据导出系统

[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)

```eval_rst
.. admonition:: **简介**

    区块链节点计算能力稀缺和KV存储的数据结构等技术特点决定了区块链上不适合进行复杂的数据查询、数据分析和数据计算等工作，上述工作更适合在链下完成。除了导出区块链的通用基础数据，例如区块高度、区块Hash、区块共识节点等，由于智能合约的不同，每个区块链项目都需要开发能够导出基于自身合约业务数据的应用，存在着大量工程、时间的重复和浪费。因此，我们致力于提供一个通用化、智能化、标准化的数据导出组件。

    WeBankBlockchain-Data-Export是一款基于FISCO BCOS平台的数据导出工具，旨在降低区块链数据开发的门槛，提升研发效率。研发人员几乎不需要编写任何代码，只需要进行简单配置，就可以把非结构化的链上数据导出到关系型数据库或ES等数据源中存储，便于后续的业务分析和处理。此外，WeBankBlockchain-Data-Export还支持了多活部署、数据分库分表、导出数据可视化、应用监控等功能，适应各类复杂的业务场景，满足了业务开发中各项需求，提升了使用体验。

    WeBankBlockchain-Data-Export的前身是WeBASE-Codegen-Monkey和WeBASE-Collect-Bee，迄今已知在十多家公司数十个生产系统中稳定、安全运行。作为区块链数据治理的关键一环，WeBankBlockchain-Data-Export基于此进行了整合和优化，并作为WeBankBlockchain-Data重要组件正式发布。后续的社区和支持会转移到WeBankBlockchain-Data。欢迎大家一起多多反馈并参与到建设中来。

    WeBankBlockchain-Data-Export提供服务和SDK调用两种使用方式，通过jar包依赖可以集成到用户项目中，使用更加灵活便捷。

```

```eval_rst
.. image:: ../../images/overview/Data-Export.png
```

```eval_rst
.. admonition:: **主要特性**

    - 使用方式支持通过服务启动和通过SDK调用
    - 自动基于智能合约代码分析和生成导出代码
    - 支持自定义导出数据内容
    - 内置Restful API，提供常用的查询功能
    - 支持多数据源，如MySQL、ElasticSearch
    - 海量数据导出，支持读写分离和分库分表
    - 支持多活部署，多节点自动导出
    - 支持区块重置导出
    - 支持可视化的监控页面
    - 提供可视化的互动API控制台
    - SDK支持JSON-RPC、Channel通道、数据仓库源等方式导出数据
```

```eval_rst
.. toctree::
   :maxdepth: 3

   outline.md
   install.md
   config.md
   model.md
   question.md
   appendix.md
```
