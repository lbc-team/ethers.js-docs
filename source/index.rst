ethers.js 翻译及说明
**************************

本文档基于当前最新 `官方文档 <https://docs.ethers.io/ethers.js/html/>`_ 由 `深入浅出区块链  <https://learnblockchain.cn/>`_ 社区成员翻译、整理、校队，我们虽力求准确，但如您发现纰漏，欢迎到 `GitHub 提Issues  <https://github.com/lbc-team/web3.js-0.2x-docs>`_ 指正。

尊重汗水，需转载请联系微信：xlbxiong 获取授权。

The ethers.js library aims to be a complete and compact library for interacting
with the Ethereum Blockchain and its ecosystem. It was originally designed for
use with `ethers.io`_ and has since expanded into a much more general-purpose
library.


包含功能
==========

    - Keep your private keys in your client, **safe** and sound
    - Import and export **JSON wallets** (Geth, Parity and crowdsale)
    - Import and export BIP 39 **mnemonic phrases** (12 word backup phrases) and HD Wallets (English, Italian, Japanese, Korean, Simplified Chinese, Traditional Chinese; more coming soon)
    - Meta-classes create JavaScript objects from any contract ABI, including **ABIv2** and **Human-Readable** ABI
    - Connect to Ethereum nodes over `JSON-RPC`_, `INFURA`_, `Etherscan`_, or `MetaMask`_.
    - **ENS names** are first-class citizens; they can be used anywhere an Ethereum addresses can be used
    - **Tiny** (~88kb compressed; 284kb uncompressed)
    - **Complete** functionality for all your Ethereum needs
    - Extensive `documentation`_
    - Large collection of **test cases** which are maintained and added to
    - Fully **TypeScript** ready, with definition files and full TypeScript source
    - **MIT License** (including ALL dependencies); completely open source to do with as you please


.. toctree::
   :maxdepth: 4
   :caption: 开发手册

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
.. _documentation: https://docs.ethers.io

Indices and tables
==================

* :ref:`genindex`
* :ref:`search`


.. EOF
