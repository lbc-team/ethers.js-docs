ethers.js 翻译及说明
**************************

翻译说明
==========

本文档基于当前最新 `官方文档 <https://docs.ethers.io/ethers.js/html/>`_ 由 `深入浅出区块链  <https://learnblockchain.cn/>`_ 社区成员翻译、整理、校队，我们虽力求准确，但如您发现纰漏，欢迎到 `GitHub 提Issues  <https://github.com/lbc-team/ethers.js-docs>`_ 指正。

尊重汗水，需转载请联系微信：xlbxiong 获取授权。


ethers.js 介绍
====================

ethers.js库旨在为以太坊区块链及其生态系统提供一个小而完整的 JavaScript API 库
它最初是与 `ethers.io`_ 一起使用，现在已经扩展为更通用的库。

.. note::
    译者注：Tiny熊 `用 ethers.js 开发了网页钱包`_ ，是一个很好的学习案例。 

包含功能
==========

    - 将私钥保存在客户端，**安全** 可信赖
    - 可支持导入和导出的 **JSON钱包文件** （Geth，Parity和crowdsale）
    - 导入和导出 BIP39 **助记词** （12个词备份短语）和 HD（分层确定性）钱包（英语，意大利语，日语，韩语，简体中文，繁体中文; 更多即将推出）
    - 从任何合同ABI创建JavaScript 元类对象，包括 **ABIv2** 和 **可读的** ABI
    - 支持通过 `JSON-RPC`_ ， `INFURA`_ ， `Etherscan`_ 或 `MetaMask`_ 连接到以太坊节点。
    -  **ENS名称** 是“一等公民”；它们可以在任何可以使用以太坊地址的地方使用
    -  库 **非常小** （压缩~88kb;未压缩284kb）
    -  功能 **完整** ，满足所有的以太坊相关需求
    - 文档全面： `中文文档`_  及 `documentation`_ 
    - 大量维护和添加的 **测试用例**
    - 完全支持 **TypeScript** 准备好，有定义文件和完整的TypeScript源文件
    -  宽松的 **MIT 协议许可** （包括所有依赖）; 完全开源可以随意使用

.. toctree::
   :maxdepth: 4
   :caption: 开发手册目录

   getting-started
   api
   api-advanced
   cookbook
   migration
   notes
   testing


.. _ethers.io: https://ethers.io
.. _JSON-RPC: https://github.com/ethereum/wiki/wiki/JSON-RPC
.. _INFURA: https://infura.io/
.. _Etherscan: https://etherscan.io/
.. _MetaMask: https://metamask.io/
.. _中文文档: https://docs.ethers.io
.. _documentation: https://docs.ethers.io
.. _用 ethers.js 开发了网页钱包: https://learnblockchain.cn/2019/04/11/wallet-dev-guide/#ethers.js

Indices and tables
==================

* :ref:`genindex`
* :ref:`search`


.. EOF
