.. include:: glossaries.rst
.. |nbsp| unicode:: U+00A0 .. non-breaking space

.. _api-provider:

提供者 Provider 
*****************

|provider| 是一个连接以太坊网络的抽象，用与查询以太坊网络状态或者发送更改状态的交易。

*EtherscanProvider* 和 *InfuraProvider* 提供连接公开的第三方节点服务提供商，无需自己运行任何以太坊节点。

*JsonRpcProvider* 和 *IpcProvider* 允许连接到我们控制或可以访问的以太坊节点（包括主网，测试网，权威证明（PoA）节点或Ganache）。

如果你已经有 Web3 应用程序或 Web3 兼容的 |provider| （例如 MetaMask 的 web3.currentProvider ），它可以用 *Web3Provider* 包装来使它与ethers的Provider API 兼容。

在大多数情况下，建议使用默认 |provider| ， 它同时连接 Etherscan 和 INFURA 。


.. code-block:: javascript
    :caption: *连接默认的 provider*

    // 可以使用任何标准网络名称做参数： 
    //  - "homestead"
    //  - "rinkeby"
    //  - "ropsten"
    //  - "kovan"
    //  - "goerli"

    let provider = ethers.getDefaultProvider('ropsten');

.. code-block:: javascript
    :caption: *连接 MetaMask*

    // 将自动检测网络; 
    // 如果在MetaMask中更改了网络，则会导致页面刷新。

    let provider = new ethers.providers.Web3Provider(web3.currentProvider);

-----

.. _provider-connect:

连接以太坊网络
======================

有几种方法可以连接到以太坊提供的网络。如果你不是运行您自己的本地以太坊节点，建议使用 ``getDefaultProvider()`` 方法。


:sup:`ethers` . getDefaultProvider( [ network :sup:`= "homestead"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Provider`
    
    创建（返回）一个由多个后端（INFURA 和 Etherscan）支持的 FallbackProvider 。
    如果你不是运行自己的以太坊节点，这是**推荐**的连接以太坊网络的方法

new :sup:`ethers . providers` . EtherscanProvider( [ network :sup:`= "homestead"` ] [ , apiToken ] )
    连接 `Etherscan`_ blockchain `web 服务 API <https://etherscan.io/apis>`_.

    **参考:** Etherscan 说明： :ref:`属性 <provider-etherscan-properties>` 和 :ref:`方法 <provider-etherscan-extra>`

new :sup:`ethers . providers` . InfuraProvider( [ network :sup:`= "homestead"` ] [ , apiAccessToken ] )
    连接 `INFURA`_ 提供的以太坊节点。

    **参考:** INFURA 说明： :ref:`属性 <provider-infura-properties>`

new :sup:`ethers . providers` . JsonRpcProvider( [ urlOrInfo :sup:`= "http://localhost:8545"` ] [ , network ] )
    通过节点 *urlorInfo* 的 `JSON-RPC API`_ URL 进行连接, 如： `Parity`_ 或 `Geth`_ 。

    参数： *urlOrInfo* 也可以作为一个对象，可以指定以下参数：

    - **url** --- JSON-RPC URL (必须)
    - **user** --- 用于基本身份验证的用户名 (可选)
    - **password** --- 用于基本身份验证的密码 (可选)
    - **allowInsecure** --- 允许通过不安全的HTTP网络进行基本身份验证 (默认值: false)

    **参考:** JSON-RPC 说明： :ref:`属性 <provider-jsonrpc-properties>` 和 :ref:`方法 <provider-jsonrpc-extra>`

new :sup:`ethers . providers` . Web3Provider( web3Provider [ , network ] )
    连接到现有的 Web3 提供者 (如： `web3Instance.currentProvider`).

    如果没指定参数 *network* ，也会自动检测网络 *network* （主网还是测试网）; 有关详细信息见上文 JsonRpcProvider 的 *network* 说明。

    **参考:** Web3 说明： :ref:`属性 <provider-web3-properties>` 和 :ref:`方法 <provider-jsonrpc-extra>`

new :sup:`ethers . providers` . FallbackProvider( providers )
    通过依次尝试每个 |provider| 来提高可靠性，如果遇到错误，则返回列表中的下一个提供程序。 网络由 |provider|  确定，**必须** 相互匹配。

    **参考:** Fallback 说明： :ref:`属性 <provider-fallback-properties>`

new :sup:`ethers . providers` . IpcProvider( path [ , network ] )
    通过节点 IPC  `JSON-RPC API`_ 连接节点， 如： `Parity`_ 或 `Geth`_ 。提供一个 IPC路径 *path* such

    如果没指定参数 *network* ，也会自动检测网络 *network* （主网还是测试网）; 有关详细信息见上文 JsonRpcProvider 的 *network* 说明。

    **参考:** IPC 说明： :ref:`属性 <provider-ipc-properties>` 和 :ref:`方法 <provider-jsonrpc-extra>`

.. code-block:: javascript
    :caption: *连接第三方提供者*

    // 可以使用任何标准网络名称做参数： 
    //  - "homestead"
    //  - "rinkeby"
    //  - "ropsten"
    //  - "kovan"

    let defaultProvider = ethers.getDefaultProvider('ropsten');

    // ... 或 ...

    let etherscanProvider = new ethers.providers.EtherscanProvider('ropsten');

    // ... 或 ...

    let infuraProvider = new ethers.providers.InfuraProvider('ropsten');

.. code-block:: javascript
    :caption: *连接 Geth 或 Parity 节点*

    // 在使用JSON-RPC API时，将自动检测网络 


    // 默认: http://localhost:8545
    let httpProvider = new ethers.providers.JsonRpcProvider();


    // 通过定制 URL 连接 :
    let url = "http://something-else.com:8546";
    let customHttpProvider = new ethers.providers.JsonRpcProvider(url);


    // 通过 IPC 命名管道
    let path = "/var/run/parity.ipc";
    let ipcProvider = new ethers.providers.IpcProvider(path);


.. code-block:: javascript
    :caption: *连接一个已有的 Web3 提供者*

    // 使用 Web3 provider 时, 自动检测网络

    // e.g. HTTP provider
    let currentProvider = new web3.providers.HttpProvider('http://localhost:8545');

    let web3Provider = new ethers.providers.Web3Provider(currentProvider);

-----

属性
==========

除非另有说明，否则所有属性都是不可变的，如果未指定，则将采用默认值。

.. _provider:

基类 Provider 属性
-------------------

:sup:`prototype` . blockNumber

    返回 |provider| 已经知晓的最新区块号（块高），如果没有同步到区块，则为 *null*。

:sup:`prototype` . polling
    *可变的 mutable*

    如果 |provider| 当前正在轮询，因为它活跃的观察事件。
    轮询可以设置为临时启用/禁用或永久禁用以允许节点进程退出。

:sup:`prototype` . pollingInterval
    *可变的 mutable*

    |provider| 的轮询频率（以毫秒为单位）。 默认时间间隔为4秒。

    对于 PoA 网络本地节点时，更小的轮询间隔也许有意义。 不过但轮询Etherscan或INFURA时，设置得太低可能会导致服务阻止我们的IP地址或以其他方式限制API调用。

.. _provider-etherscan-properties:

EtherscanProvider （派生于Provider）属性
-------------------------------------------------------

:sup:`prototype` . apiToken
    The Etherscan API Token (如果没有指定则为空)

.. _provider-infura-properties:

InfuraProvider （派生于 JsonRpcProvider ）属性
-------------------------------------------------------

:sup:`prototype` . apiAccessToken
    The INFURA API Access Token (如果没有指定则为空)


.. _provider-jsonrpc-properties:

JsonRpcProvider （派生于Provider）属性
-----------------------------------------------------

:sup:`prototype` . connection
    描述JSON-RPC 节点与属性的连接对象：

    - **url** --- the JSON-RPC URL
    - **user** --- 用于基本身份验证的用户名  (可选)
    - **password** --- 用于基本身份验证的密码 (可选)
    - **allowInsecure** --- 允许通过不安全的HTTP网络进行基本身份验证

.. _provider-web3-properties:

Web3Provider （派生于 JsonRpcProvider ）属性
-----------------------------------------------------

:sup:`prototype` . provider
    与 Web3 库兼用的底层 |provider| 。 比如： `HTTPProvider`_ 或 `IPCProvider`_ 。 Web3 |provider| 唯一需要的方法是:

    - **sendAsync ( method , params , callback )**

.. _provider-fallback-properties:

FallbackProvider （派生于Provider）属性
------------------------------------------------------

:sup:`prototype` . providers
    一组 |provider| 的拷贝(修改此变量不会影响关联的 |provider| )。


.. _provider-ipc-properties:

IpcProvider （派生于 JsonRpcProvider ）属性
----------------------------------------------------

:sup:`prototype` . path
    返回连接的 JSON-RPC IPC（命名管道）路径。


-----

.. _provider-network:

获取网络
=========

:sup:`prototype` . getNetwork ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Network>`
    返回可获取 :ref:`网络 <network>` 对象的 :ref:`Promise <promise>` ，包含了连接的网络和链信息。

-----

.. _provider-account:

获取账号信息
==============

:sup:`prototype` . getBalance ( addressOrName [ , blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    返回参数 *addressOrName* 余额的（类型为 :ref:`BigNumber <bignumber>` ） :ref:`Promise <promise>` 对象。
    可选参数 *blockTag* (参考: :ref:`Block Tags <blocktag>`) 指定一个区块。

:sup:`prototype` . getTransactionCount ( addressOrName [ , blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<number>`
    返回参数 *addressOrName* 的所发送交易数量（Number对象）的 :ref:`Promise <promise>` 对象，它用来作为发送交易的nonce。
    可选参数 *blockTag* 指定一个区块， (参考: :ref:`Block Tags <blocktag>`) 。


.. code-block:: javascript
    :caption: *获取账号余额*

    let address = "0x02F024e0882B310c6734703AB9066EdD3a10C6e0";

    provider.getBalance(address).then((balance) => {

        // 余额是 BigNumber (in wei); 格式化为 ether 字符串
        let etherString = ethers.utils.formatEther(balance);

        console.log("Balance: " + etherString);
    });

.. code-block:: javascript
    :caption: *获取账号所发送交易数量*

    let address = "0x02F024e0882B310c6734703AB9066EdD3a10C6e0";

    provider.getTransactionCount(address).then((transactionCount) => {
        console.log("发送交易总数: " + transactionCount);
    });

-----

.. _provider-blockchain:

获取以太坊状态
=================

:sup:`prototype` . getBlockNumber ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<number>`
    返回最新区块号（Number类型）的 :ref:`Promise <promise>` 。

:sup:`prototype` . getGasPrice ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    返回当前 gas 价格  (类型 :ref:`BigNumber <bignumber>`) 的 :ref:`Promise <promise>` 。

:sup:`prototype` . getBlock ( blockHashOrBlockNumber ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Block>`
    返回给定参数对应的区块信息的 :ref:`Promise <promise>`  (参考: :ref:`区块回复 <blockresponse>`) 。

:sup:`prototype` . getTransaction ( transactionHash ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    根据交易hash获取交易信息的 :ref:`Promise <promise>` 。 (参考: :ref:`交易回复 <transaction-response>`)

:sup:`prototype` . getTransactionReceipt ( transactionHash ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionReceipt>`
    根据交易hash获取交易收据的 :ref:`Promise <promise>` 
    (参考: :ref:`交易收据 <transaction-receipt>`)


.. code-block:: javascript
    :caption: *获取当前状态*

    provider.getBlockNumber().then((blockNumber) => {
        console.log("Current block number: " + blockNumber);
    });

    provider.getGasPrice().then((gasPrice) => {
        // gasPrice is a BigNumber; convert it to a decimal string
        gasPriceString = gasPrice.toString();

        console.log("Current gas price: " + gasPriceString);
    });

.. code-block:: javascript
    :caption: *区块*

    // 查看: https://ropsten.etherscan.io/block/3346773

    // 区块号
    provider.getBlock(3346773).then((block) => {
        console.log(block);
    });

    // 区块 Hash
    let blockHash = "0x7a1d0b010393c8d850200d0ec1e27c0c8a295366247b1bd6124d496cf59182ad";
    provider.getBlock(blockHash).then((block) => {
        console.log(block);
    });

.. code-block:: javascript
    :caption: *交易*

    // 查看: https://ropsten.etherscan.io/tx/0xa4ddad980075786c204b45ab8193e543aec4411bd94894abef47dc90d4d3cc01

    let transactionHash = "0xa4ddad980075786c204b45ab8193e543aec4411bd94894abef47dc90d4d3cc01"

    provider.getTransaction(transactionHash).then((transaction) => {
        console.log(transaction);
    });

    provider.getTransactionReceipt(transactionHash).then((receipt) => {
        console.log(receipt);
    });

-----

.. _provider-ens:

以太坊域名服务 ENS 
=======================

 `Ethereum Naming Service`_ (ENS：Ethereum Naming Service 以太坊域名服务) 允许使用一个容易记住的名称来关联一个以太坊地址。（译者注：类似于域名和IP地址）
 任何提供者的可以针对地址也可以针对ENS名称

ENS还提供反向查找功能，如果已经已配置，可以根据名称找到地址。

:sup:`prototype` . resolveName ( ensName ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Address>`
    获取  *ensName* 对应地址的 :ref:`Promise <promise>` ，如果没有则为null 。

:sup:`prototype` . lookupAddress ( address ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<string>`
    获取  *address* 对应的  ENS 名称的 :ref:`Promise <promise>` ，如果没有则为null 。

.. code-block:: javascript
    :caption: *将ENS名称解析为地址*

    provider.resolveName("registrar.firefly.eth").then(function(address) {
        console.log("Address: " + address);
        // "0x6fC21092DA55B392b045eD78F4732bff3C580e2c"
    });

.. code-block:: javascript
    :caption: *查找地址的ENS名称*

    let address = "0x6fC21092DA55B392b045eD78F4732bff3C580e2c";
    provider.lookupAddress(address).then(function(address) {
        console.log("Name: " + address);
        // "registrar.firefly.eth"
    });

-----

.. _provider-calling:

执行合约
==================

这些是相对低级别的调用。通常应该使用 :ref:`合约 API <api-contract>`  

:sup:`prototype` . call ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    Send the **read-only** (constant) *transaction* to a single Ethereum node and
    return a :ref:`Promise <promise>` with the result (as a :ref:`hex string <hexstring>`) of executing it.
    (See :ref:`Transaction Requests <transaction-request>`)

    免费执行，因为它不会改变区块链上的任何状态。

:sup:`prototype` . estimateGas ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    Send a *transaction* to a single Ethereum node and return a :ref:`Promise <promise>` with the
    estimated amount of gas required (as a :ref:`BigNumber <bignumber>`) to send it.
    (See :ref:`Transaction Requests <transaction-request>`)

    免费执行，但只是一个估算。 提供太少的 Gas 将导致交易被拒绝（同时仍然消耗掉所有提供的Gas）。

:sup:`prototype` . sendTransaction ( signedTransaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    Send the *signedTransaction* to the **entire** Ethereum network and 返回 :ref:`Promise <promise>`
    that resolves to the :ref:`Transaction Response <transaction-response>`.

    If an error occurs after the netowrk **may have** received the transaction, the
    promise will reject with the error, with the additional property ``transactionHash``
    so that further processing may be done.

    **This will consume gas** from the account that signed the transaction.


.. code-block:: javascript
    :caption: *calling constant functions*

    // See: https://ropsten.etherscan.io/address/0x6fc21092da55b392b045ed78f4732bff3c580e2c

    // Setup a transaction to call the FireflyRegistrar.fee() function

    // FireflyRegistrar contract address
    let address = "0x6fC21092DA55B392b045eD78F4732bff3C580e2c";

    // First 4 bytes of the hash of "fee()" for the sighash selector
    let data = ethers.utils.hexDataSlice(ethers.utils.id('fee()'), 0, 4);

    let transaction = {
        to: ensName,
        data: data
    }

    let callPromise = defaultProvider.call(transaction);

    callPromise.then((result) => {
        console.log(result);
        // "0x000000000000000000000000000000000000000000000000016345785d8a0000"

        console.log(ethers.utils.formatEther(result));
        // "0.1"
    });

.. code-block:: javascript
    :caption: *sending a transaction*

    let privateKey = '0x0123456789012345678901234567890123456789012345678901234567890123';
    let wallet = new ethers.Wallet(privateKey, provider);

    let transaction = {
        to: "ricmoo.firefly.eth",
        value: ethers.utils.parseEther("0.1")
    };

    // Send the transaction
    let sendTransactionPromise = wallet.sendTransaction(transaction);

    sendTransactionPromise.then((tx) => {
       console.log(tx);
    });

-----

.. _provider-contract:

合约信息
==============

:sup:`prototype` . getCode ( addressOrName ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    返回 :ref:`Promise <promise>` with the bytecode (as a :ref:`hex string <hexstring>`)
    at  *addressOrName*.

:sup:`prototype` . getStorageAt ( addressOrName , position [ , blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    返回 :ref:`Promise <promise>` with the value (as a :ref:`hex string <hexstring>`) at
    *addressOrName* in *position* at *blockTag*. (参考 :ref:`Block Tags <blocktag>`)

:sup:`prototype` . getLogs ( filter ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise< Log [ ] >`
    返回 :ref:`Promise <promise>` with an array (possibly empty) of the logs that
    match the *filter*. (参考 :ref:`Filters <filter>`)

.. code-block:: javascript
    :caption: *get contract code*

    let contractEnsName = 'registrar.firefly.eth';

    let codePromise = provider.getCode(contractEnsName);

    codePromise.then((result) => {
       console.log(result);
    });

.. code-block:: javascript
    :caption: *get contract storage value*

    let contractEnsName = 'registrar.firefly.eth';

    // Position 0 in the FireflyRegistrar contract holds the ENS address

    let storagePromise = provider.getStorageAt(contractEnsName, 0);

    storagePromise.then((result) => {
       console.log(result);
       // "0x000000000000000000000000112234455c3a32fd11230c42e7bccd4a84e02010"
    });

.. code-block:: javascript
    :caption: *get contract event logs*

    let contractEnsName = 'registrar.firefly.eth';

    let topic = ethers.utils.id("nameRegistered(bytes32,address,uint256)");

    let filter = {
        address: contractEnsName,
        fromBlock: 3313425,
        toBlock: 3313430,
        topics: [ topic ]
    }

    provider.getLogs(filter).then((result) => {
        console.log(result);
        // [ {
        //    blockNumber: 3313426,
        //    blockHash: "0xe01c1e437ed3af9061006492cb07454eca8561479454a709809b7897f225387d",
        //    transactionIndex: 5,
        //    removed: false,
        //    address: "0x6fC21092DA55B392b045eD78F4732bff3C580e2c",
        //    data: "0x00000000000000000000000053095760c154a1531a69fc718119d14c4aa1506f" +
        //            "000000000000000000000000000000000000000000000000016345785d8a0000",
        //    topics: [
        //      "0x179ef3319e6587f6efd3157b34c8b357141528074bcb03f9903589876168fa14",
        //      "0xe625ed7b108857745d1d9889a7ae05861d8aee38e0e92fd3a31191de01c2515b"
        //    ],
        //    transactionHash: "0x61d641aaf3dcf4cf6bafc3e79d332d8773ea0688f87eb00f8b60c3f0050e55f0",
        //    logIndex: 5
        // } ]

    });

-----

.. _provider-events:

事件
======

These methods allow management of callbacks on certain events on the blockchain
and contracts. They are largely based on the `EventEmitter API`_.

:sup:`prototype` . on ( eventType , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`Provider`
    Register a callback for any future *eventType*; see below for callback parameters

:sup:`prototype` . once ( eventType , callback) |nbsp| :sup:`=>` |nbsp| :sup:`Provider`
    Register a callback for the next (and only next) *eventType*; see below for callback parameters

:sup:`prototype` . removeListener ( eventType , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`boolean`
    Unregister the *callback* for *eventType*; if the same callback is registered
    more than once, only the first registered instance is removed

:sup:`prototype` . removeAllListeners ( eventType ) |nbsp| :sup:`=>` |nbsp| :sup:`Provider`
    Unregister all callbacks for *eventType*

:sup:`prototype` . listenerCount ( [ eventType ] ) |nbsp| :sup:`=>` |nbsp| :sup:`number`
    Return the number of callbacks registered for *eventType*, or if ommitted, the
    total number of callbacks registered

:sup:`prototype` . resetEventsBlock ( blockNumber ) |nbsp| :sup:`=>` |nbsp| :sup:`void`
    Begin scanning for events from *blockNumber*. By default, events begin at the
    block number that the provider began polling at.

事件类型
-----------

"block"
    Whenever a new block is mined

    ``callback( blockNumber )``

"pending"
    Whenever a new transaction is added to the transaction pool. This is **NOT**
    available on Etherscan or INFURA providers and may not be reliable on any
    provider.

    ``callback( transactionHash )``

"error"
    Whenever an error occurs during an event.

    ``callback( error )``

any address
    When the balance of the corresponding address changes.

    ``callback( balance )``

any transaction hash
    When the corresponding transaction has been included in a block; 参考
    :ref:`Waiting for Transactions <waitForTransaction>`.

    ``callback( transactionReceipt )``

a filtered event object
    When the an event is logged by a transaction to the *address* with the
    associated *topics*. The filtered event properties are:

    - **address** --- the contract address to filter by (可选)
    - **topics** --- the log topics to filter by (可选)

    ``callback( log )``

an array of topics
    When any of the topics are logs by a transaction to any address. This is
    equivalent to using a filter object with no *address*.

    ``callback( log )``

.. _waitForTransaction:

等待交易
------------------------

:sup:`prototype` . waitForTransaction ( transactionHash ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionReceipt>`
    Return a :ref:`Promise <promise>` 可以获取到 the
    :ref:`Transaction Receipt <transaction-receipt>` once *transactionHash* is
    mined.

.. code-block:: javascript
    :caption: *new blocks*

    provider.on('block', (blockNumber) => {
        console.log('New Block: ' + blockNumber);
    });

.. code-block:: javascript
    :caption: *account balance changes*

    provider.on('0x46Fa84b9355dB0708b6A57cd6ac222950478Be1d', (balance) => {
        console.log('New Balance: ' + balance);
    });

.. code-block:: javascript
    :caption: *transaction mined*

    provider.once(transactionHash, (receipt) => {
        console.log('Transaction Minded: ' + receipt.hash);
        console.log(receipt);
    );

    // ... OR ...

    provider.waitForTransaction(transactionHash).then((receipt) => {
        console.log('Transaction Mined: ' + receipt.hash);
        console.log(receipt);
    });

.. code-block:: javascript
    :caption: *a filtered event has been logged*

    let contractEnsName = 'registrar.firefly.eth';

    let topic = ethers.utils.id("nameRegistered(bytes32,address,uint256)");

    let filter = {
        address: contractEnsName,
        topics: [ topic ]
    }

    provider.on(filter, (result) => {
        console.log(result);
        // {
        //    blockNumber: 3606106,
        //    blockHash: "0x878aa7059c93239437f66baeec82332dcb2f9288bcdf6eb1ff3ba6998cdf8f69",
        //    transactionIndex: 6,
        //    removed: false,
        //    address: "0x6fC21092DA55B392b045eD78F4732bff3C580e2c",
        //    data: "0x00000000000000000000000006b5955a67d827cdf91823e3bb8f069e6c89c1d6" +
        //            "000000000000000000000000000000000000000000000000016345785d8a0000",
        //    topics: [
        //      "0x179ef3319e6587f6efd3157b34c8b357141528074bcb03f9903589876168fa14",
        //      "0x90a4d0958790016bde1de8375806da3be227ff48e611aefea36303fb86bca5ad"
        //    ],
        //    transactionHash: "0x0d6f43accb067ca8e391666f37f8e8ad75f88ebd8036c9166fd2d0b93b214d2e",
        //    logIndex: 6
        // }
    });


-----

对象及类型（用于参数及返回值）
==================================

There are several common objects and types that are commonly used as input parameters or
return types for various provider calls.

-----

.. _blocktag:

Block Tag
---------

A block tag is used to uniquely identify a block's position in the blockchain:

a Number or :ref:`hex string <hexstring>`:
    Each block has a block number (eg. ``42`` or ``"0x2a``.

"latest":
    The most recently mined block.

"pending":
    The block that is currently being mined.

-----

.. _blockresponse:

Block Responses
---------------

.. code-block:: javascript
    :caption: *Example*

    {
        parentHash: "0x3d8182d27303d92a2c9efd294a36dac878e1a9f7cb0964fa0f789fa96b5d0667",
        hash: "0x7f20ef60e9f91896b7ebb0962a18b8defb5e9074e62e1b6cde992648fe78794b",
        number: 3346463,

        difficulty: 183765779077962,
        timestamp: 1489440489,
        nonce: "0x17060cb000d2c714",
        extraData: "0x65746865726d696e65202d20555331",

        gasLimit: utils.bigNumberify("3993225"),
        gasUsed: utils.bigNuberify("3254236"),

        miner: "0xEA674fdDe714fd979de3EdF0F56AA9716B898ec8",
        transactions: [
            "0x125d2b846de85c4c74eafb6f1b49fdb2326e22400ae223d96a8a0b26ccb2a513",
            "0x948d6e8f6f8a4d30c0bd527becbe24d15b1aba796f9a9a09a758b622145fd963",
            ... [ 49 more transaction hashes ] ...
            "0xbd141969b164ed70388f95d780864210e045e7db83e71f171ab851b2fba6b730"
        ]
    }

-----

.. _network:

网络
-------

A network repsents various properties of a network, such as mainnet (i.e. "homestead") or
one of the testnets (e.g. "ropsten", "rinkeby" or "kovan") or alternative networks
(e.g. "classic"). A Network has the following properties:

    - *name* --- the name of the network (e.g. "homestead", "rinkeby")
    - *chainId* --- the chain ID (network ID) of the connected network
    - *ensAddress* --- the address of ENS if it is deployed to the network, otherwise *null*

If a network does not have the ENS contract deployed to it, names cannot be resolved to addresses.

.. code-block:: javascript
    :caption: *get a standard network*

    let network = ethers.providers.getNetwork('homestead');
    // {
    //    chainId: 1,
    //    ensAddress: "0x314159265dd8dbb310642f98f50c066173c1259b",
    //    name: "homestead"
    // }

.. code-block:: javascript
    :caption: *a custom development network*

    let network = {
        chainId: 1337,
        name: "dev"
    }

-----

.. _transaction-request:

交易请求
--------------------

Any property which accepts a number may also be specified as a :ref:`BigNumber <bignumber>`
or :ref:`hex string <hexstring>`. Any property may also be given as a :ref:`Promise <promise>`
可以获取到 the expected type.

.. code-block:: javascript
    :caption: *Example*

    {
        // Required unless deploying a contract (in which case omit)
        to: addressOrName,  // the target address or ENS name

        // These are optional/meaningless for call and estimateGas
        nonce: 0,           // the transaction nonce
        gasLimit: 0,        // the maximum gas this transaction may spend
        gasPrice: 0,        // the price (in wei) per unit of gas

        // These are always optional (but for call, data is usually specified)
        data: "0x",         // extra data for the transaction, or input for call
        value: 0,           // the amount (in wei) this transaction is sending
        chainId: 3          // the network ID; usually added by a signer
    }

-----

.. _transaction-response:

交易回复
--------------------

.. code-block:: javascript
    :caption: *Example*

    {
        // Only available for mined transactions
        blockHash: "0x7f20ef60e9f91896b7ebb0962a18b8defb5e9074e62e1b6cde992648fe78794b",
        blockNumber: 3346463,
        timestamp: 1489440489,

        // Exactly one of these will be present (send vs. deploy contract)
        // They will always be a properly formatted checksum address
        creates: null,
        to: "0xc149Be1bcDFa69a94384b46A1F91350E5f81c1AB",

        // The transaction hash
        hash: "0xf517872f3c466c2e1520e35ad943d833fdca5a6739cfea9e686c4c1b3ab1022e",

        // See above "Transaction Requests" for details
        data: "0x",
        from: "0xEA674fdDe714fd979de3EdF0F56AA9716B898ec8",
        gasLimit: utils.bigNumberify("90000"),
        gasPrice: utils.bigNumberify("21488430592"),
        nonce: 0,
        value: utils.parseEther(1.0017071732629267),

        // The chain ID; 0 indicates replay-attack vulnerable
        // (eg. 1 = Homestead mainnet, 3 = Ropsten testnet)
        chainId: 1,

        // The signature of the transaction (TestRPC may fail to include these)
        r: "0x5b13ef45ce3faf69d1f40f9d15b0070cc9e2c92f3df79ad46d5b3226d7f3d1e8",
        s: "0x535236e497c59e3fba93b78e124305c7c9b20db0f8531b015066725e4bb31de6",
        v: 37,

        // The raw transaction (TestRPC may be missing this)
        raw: "0xf87083154262850500cf6e0083015f9094c149be1bcdfa69a94384b46a1f913" +
               "50e5f81c1ab880de6c75de74c236c8025a05b13ef45ce3faf69d1f40f9d15b0" +
               "070cc9e2c92f3df79ad46d5b3226d7f3d1e8a0535236e497c59e3fba93b78e1" +
               "24305c7c9b20db0f8531b015066725e4bb31de6"
    }

-----

.. _transaction-receipt:

交易收据
--------------------

.. code-block:: javascript
    :caption: *Example*

    {
        transactionHash: "0x7dec07531aae8178e9d0b0abbd317ac3bb6e8e0fd37c2733b4e0d382ba34c5d2",

        // The block this transaction was mined into
        blockHash: "0xca1d4d9c4ac0b903a64cf3ae3be55cc31f25f81bf29933dd23c13e51c3711840",
        blockNumber: 3346629,

        // The index into this block of the transaction
        transactionIndex: 1,

        // The address of the contract (if one was created)
        contractAddress: null,

        // Gas
        cumulativeGasUsed: utils.bigNumberify("42000"),
        gasUsed: utils.bigNumberify("21000"),

        // Logs (an Array of Logs)
        log: [ ],
        logsBloom: "0x00" ... [ 256 bytes of 0 ] ... "00",

        // Post-Byzantium hard-fork
        byzantium: false

        ////////////
        // Pre-byzantium blocks will have a state root:
        root: "0x8a27e1f7d3e92ae1a01db5cce3e4718e04954a34e9b17c1942011a5f3a942bf4",

        ////////////
        // Post-byzantium blocks will have a status (0 indicated failure during execution)
        // status: 1
    }

-----

.. _log:

日志
------

.. code-block:: javascript
    :caption: *Example*

    {
        // The block this log was emitted by
        blockNumber: 
        blockHash:

        // The transaction this log was emiited by
        transactionHash:
        transactionIndex:
        logIndex:

        // Whether the log has been removed (due to a chain re-org)
        removed: false,

        // The contract emitting the log
        address:

        // The indexed data (topics) and non-indexed data (data) for this log
        topics: []
        data: 
    }

-----

.. _filter:

过滤器
---------

Filtering on topics supports a `somewhat complicated`_ specification, however,
for the vast majority of filters, a single topic is usually sufficient (see the example below).

The *EtherscanProvider* currently only supports a single topic.

.. code-block:: javascript
    :caption: *Example*

    {
        // Optional; The range of blocks to limit querying (See: Block Tags above)
        fromBlock: "latest",
        toBlock: "latest",

        // Optional; The specific block to limit the query to
        // Note: This may NOT be used with fromBlock or toBlock
        // Note: EtherscanProvider does not support blockHash
        // Note: This may be used for getLogs, but not as a provider Event (i.e. .on)
        blockHash: blockHash,

        // Optional; An address (or ENS name) to filter by
        address: addressOrName,

        // Optional; A (possibly nested) list of topics
        topics: [ topic1 ]
    }

@TODO: Link to cookbook entry for filtering ERC-20 events for an address

-----

|provider| 额外的 API
=================================

.. _provider-etherscan-extra:

Etherscan
---------

:sup:`prototype` . getEtherPrice ( )
    返回 :ref:`Promise <promise>` with the price of ether in USD.

:sup:`prototype` . getHistory ( addressOrName [ , startBlock :sup:`= 0` [ , endBlock :sup:`= "latest"` ] ] )
    返回 :ref:`Promise <promise>` with an array of :ref:`Transaction Responses <transaction-response>`
    for each transaction to or from *addressOrName* between *startBlock* and *endBlock* (inclusive).

.. code-block:: javascript
    :caption: *a filtered event has been logged*

    let etherscanProvider = new ethers.providers.EtherscanProvider();

    // Getting the current Ethereum price
    etherscanProvider.getEtherPrice().then(function(price) {
        console.log("Ether price in USD: " + price);
    });


    // Getting the transaction history of an address
    let address = '0xb2682160c482eB985EC9F3e364eEc0a904C44C23';
    let startBlock = 3135808;
    let endBlock = 5091477;
    etherscanProvider.getHistory(address, startBlock, endBlock).then(function(history) {
        console.log(history);
        // [
        //   {
        //     hash: '0x327632ccb6d7bb47b455383e936b2f14e6dc50dbefdc214870b446603b468675',
        //     blockHash: '0x0415f0d2741de45fb748166c7dc2aad9b3ff66bcf7d0a127f42a71d3e286c36d',
        //     blockNumber: 3135808,
        //     transactionIndex: 1,
        //     from: '0xb2682160c482eB985EC9F3e364eEc0a904C44C23',
        //     gasPrice: ethers.utils.bigNumberify('0x4a817c800'),
        //     gasLimit: ethers.utils.bigNumberify('0x493e0'),
        //     to: '0xAe572713CfE65cd7033774170F029B7219Ee7f70',
        //     value: ethers.utils.bigNumberify('0xd2f13f7789f0000'),
        //     nonce: 25,
        //     data: '0x',
        //     creates: null,
        //     chainId: 0
        //   },
        //   {
        //     hash: '0x7c10f2e7125a1fa5e37b54f5fac5465e8d594f89ff97916806ca56a5744812d9',
        //     ...
        //   }
        // ]
    });

.. _provider-jsonrpc-extra:

JsonRpcProvider
---------------

:sup:`prototype` . send ( method , params ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<any>`
    Send the JSON-RPC *method* with *params*. This is useful for calling
    non-standard or less common JSON-RPC methods. A :ref:`Promise <promise>` is
    returned which will resolve to the parsed JSON result.

:sup:`prototype` . listAccounts ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Address [ ] >`
    返回 :ref:`Promise <promise>` with a list of all account addresses the
    node connected to this Web3 controls.

:sup:`prototype` . getSigner( [ indexOrAddress ] ) |nbsp| :sup:`=>` |nbsp| :sup:`JsonRpcSigner`
    返回 :ref:`JsonRpcSigner <signer-jsonrpc>` attached to an account on the
    Ethereum node the Web3 object is connected to. If *indexOrAddress* is not specified,
    the first account on the node is used.


.. code-block:: javascript
    :caption: *send vendor specific JSON-RPC API*

    let hash = "0x2ddf6dd2ec23adf525dac59d7c9189b25b172d679aad951e59e232045f2c811f";
    jsonRpcProvider.send('debug_traceTransaction', [ hash ]).then((result) => {
        console.log(result);
    });

.. code-block:: javascript
    :caption: *list accounts and load the second account*

    // Get a signer for the account at index 1
    jsonRpcProvider.listAccounts().then((accounts) => {
        let signer = jsonRpcProvider.getSigner(accounts[1]);
        console.log(signer);
    });

.. _signer-jsonrpc:

JsonRpcSigner
-------------

An account from a JSON-RPC API connection the conforms to the :ref:`Signer API <signer>`.
The :ref:`getSigner <provider-jsonrpc-extra>` method of a JsonRpcProvider should be
used to instantiate these.

:sup:`prototype` . provider
    The provider that this Signer is connected to.

:sup:`prototype` . getAddress ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Address>`
    返回 :ref:`Promise <promise>` that resolves to the account address.

:sup:`prototype` . getBalance ( [ blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    返回 :ref:`Promise <promise>` for the account balance.

:sup:`prototype` . getTransactionCount ( [ blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<number>`
    返回 :ref:`Promise <promise>` for the account transaction count. This
    can be used to determine the next nonce to use for a transaction.

:sup:`prototype` . sendTransaction ( [ transactionRequest ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    返回 :ref:`Promise <promise>` that resolves to the Transaction Response for
    the sent transaction.

    If an error occurs after the netowrk **may have** received the transaction, the
    promise will reject with the error, with the additional property ``transactionHash``
    so that further processing may be done.

:sup:`prototype` . signMessage ( message ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    返回 :ref:`Promise <promise>` that resolves the signature of a signed message, in the
    :ref:`Flat Format <signature>`.

:sup:`prototype` . unlock ( password ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<boolean>`
    返回 :ref:`Promise <promise>` the resolves to true or false, depending
    on whether the account unlock was successful.

-----

.. _Ethereum Naming Service: https://ens.domains
.. _Etherscan: https://etherscan.io/apis
.. _web service API: https://etherscan.io/apis
.. _INFURA: https://infura.io
.. _Parity: https://ethcore.io/parity.html
.. _Geth: https://geth.ethereum.org
.. _JSON-RPC API: https://github.com/ethereum/wiki/wiki/JSON-RPC
.. _EventEmitter API: https://nodejs.org/dist/latest-v6.x/docs/api/events.html
.. _replay protection: https://github.com/ethereum/EIPs/issues/155
.. _somewhat complicated: https://github.com/ethereum/wiki/wiki/JSON-RPC#a-note-on-specifying-topic-filters
.. _HTTPProvider: https://github.com/ethereum/web3.js/blob/develop/lib/web3/httpprovider.js
.. _IPCProvider: https://github.com/ethereum/web3.js/blob/develop/lib/web3/ipcprovider.js

.. EOF
