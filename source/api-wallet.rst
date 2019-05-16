.. include:: glossaries.rst
.. |nbsp| unicode:: U+00A0 .. non-breaking space

.. _api-wallet:

在使用接口之前，需要先确保正确 `引入了 ethers.js <getting-started>`_ 。
 

钱包类 Wallet 和 签名器 Signer
**************************************

 |wallet| 类管理着一个公私钥对用于在以太坊网络上密码签名交易以及所有权证明。

-----

.. _wallet:

Wallet
======

 **Wallet** 实现了 :ref:`Signer API <signer>` ，因此可以在任何需要 |signer| 的地方使用 **Wallet** ，它包含了 |signer| 所有的属性。


创建 Wallet 实例
----------------------

new :sup:`Wallet` ( privateKey [ , provider ] )
    从参数 *privateKey* 私钥创建一个钱包实例， 还可以提供一个可选的 |provider| 参数用于连接节点。 

:sup:`Wallet` . createRandom ( [ options ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Wallet`
    创建一个随机钱包实例。 确保钱包（私钥）存放在安全的位置，如果丢失了就**没有办法找回钱包**。

    额外的参数 options 为：

        - **extraEntropy** --- 额外的熵加入随机源

.. _fromEncryptedJson:

:sup:`Wallet` . fromEncryptedJson ( json, password [ , progressCallback ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Wallet`
    通过解密一个 `JSON 钱包文件`_ 创建钱包实例，JSON 钱包文件，即 keystore文件， 通常来自 Geth, parity, Crowdsale 等钱包或工具，或者通过钱包加密函数 *prototype.encrypt* 创建。

:sup:`Wallet` . fromMnemonic ( mnemonic [ , path :sup:`= "m/44'/60'/0'/0/0"` [ , wordlist ] ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Wallet`
    通过助记词（及路径）创建钱包实例，助记词及路径由 `BIP-039`_ 和 `BIP-044`_ 定义，默认使用英文助记词。
    这里有一篇介绍 `BIP39及BIP44非常棒的文章 <https://learnblockchain.cn/2018/09/28/hdwallet/>`_ ，如果不了解 BIP39 及 BIP44 可以读一读。

    注意压缩过的 min版本 ``dist/ethers.min.js`` 仅支持英文版本， 其他语言的支持可以通过添加 ``<script>`` 来加入各语言的 ``dist/wordlist-*.js``

    当前支持的单词语言列表：

    ===================== =========================== =======================
    Language              node.js                     Browser
    ===================== =========================== =======================
    English (US)          ``ethers.wordlists.en``     *included*
    Italian               ``ethers.wordlists.it``     ``dist/wordlist-it.js``
    Japanese              ``ethers.wordlists.ja``     ``dist/wordlist-ja.js``
    Korean                ``ethers.wordlists.ko``     ``dist/wordlist-ko.js``
    Chinese (simplified)  ``ethers.wordlists.zh_cn``  ``dist/wordlist-zh.js``
    Chinese (traditional) ``ethers.wordlists.zh_tw``  ``dist/wordlist-zh.js``
    ===================== =========================== =======================

.. _wallet-connect:

:sup:`prototype` . connect ( provider ) |nbsp| :sup:`=>` |nbsp| :sup:`Wallet`
    从已有实例创建新的Wallet实例，并连接到新 |provider| 

|

|

.. code-block:: javascript
    :caption: *加载私钥y*

    let privateKey = "0x0123456789012345678901234567890123456789012345678901234567890123";
    let wallet = new ethers.Wallet(privateKey);

    // Connect a wallet to mainnet
    let provider = ethers.getDefaultProvider();
    let walletWithProvider = new ethers.Wallet(privateKey, provider);

.. code-block:: javascript
    :caption: *创建一个随机钱包实例*

    let randomWallet = ethers.Wallet.createRandom();


.. code-block:: javascript
    :caption: *加载JSON钱包文件*

    let data = {
        id: "fb1280c0-d646-4e40-9550-7026b1be504a",
        address: "88a5c2d9919e46f883eb62f7b8dd9d0cc45bc290",
        Crypto: {
            kdfparams: {
                dklen: 32,
                p: 1,
                salt: "bbfa53547e3e3bfcc9786a2cbef8504a5031d82734ecef02153e29daeed658fd",
                r: 8,
                n: 262144
            },
            kdf: "scrypt",
            ciphertext: "10adcc8bcaf49474c6710460e0dc974331f71ee4c7baa7314b4a23d25fd6c406",
            mac: "1cf53b5ae8d75f8c037b453e7c3c61b010225d916768a6b145adf5cf9cb3a703",
            cipher: "aes-128-ctr",
            cipherparams: {
                iv: "1dcdf13e49cea706994ed38804f6d171"
             }
        },
        "version" : 3
    };

    let json = JSON.stringify(data);
    let password = "foo";

    ethers.Wallet.fromEncryptedJson(json, password).then(function(wallet) {
        console.log("Address: " + wallet.address);
        // "Address: 0x88a5C2d9919e46F883EB62F7b8Dd9d0CC45bc290"
    });


.. code-block:: javascript
    :caption: *加载助记词*

    let mnemonic = "radar blur cabbage chef fix engine embark joy scheme fiction master release";
    let mnemonicWallet = ethers.Wallet.fromMnemonic(mnemonic);

    // Load the second account from a mnemonic
    let path = "m/44'/60'/1'/0/0";
    let secondMnemonicWallet = ethers.Wallet.fromMnemonic(mnemonic, path);

    // Load using a non-english locale wordlist (the path "null" will use the default)
    let secondMnemonicWallet = ethers.Wallet.fromMnemonic(mnemonic, null, ethers.wordlists.ko);

-----

Prototype 属性
------------------

:sup:`prototype` . address
    获得 |wallet| 地址

.. code-block:: javascript
    :caption: *获取 Wallet address 属性*

    let lbcWallet = ethers.Wallet.createRandom();
    lbcWallet.address
    // "0x863e27dbD608649d08c69F83ccA51b045721f318"
    lbcWallet.privateKey
    // "0x8bc2957edb0200357ddc30f681b5e5f235256e2812781f9b06415bbeb1e72b40"

:sup:`prototype` . privateKey
    钱包的私钥; 注意保密。

:sup:`prototype` . provider
    返回连接的 :ref:`Provider <provider>` 它允许钱包连接到以太坊网络以查询其状态并发送交易，
    如果没有连接提供者，则返回null。

    要更改 |provider| ，请使用 :ref:`connect <wallet-connect>` 方法，该方法将返回连接到新的 |provider| 的钱包 **实例** 。
    

:sup:`prototype` . mnemonic
   返回钱包的助记词，如果没有助记词，则为null。


:sup:`prototype` . path
    返回此钱包的助记词上的路径，如果没有助记词，则为null。

-----

签名方法
----------

:sup:`prototype` . sign ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<string>`
    对**交易**签名返回一个 :ref:`Promise <promise>` ，从Promise中可以获取一个签名后的交易hash :ref:`16进制字符串 <hexstring>`

    通常应该使用 `sendTransaction`_ 而不是 ``sign``, 因为它可以异步执行。

    交易的属性（字段）都是可选的，包括：

        - **to**
        - **gasLimit**
        - **gasPrice**
        - **nonce**
        - **data**
        - **value**
        - **chainId**

:sup:`prototype` . signMessage ( message ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<string>`
    对 *message* 签名返回 :ref:`Promise <promise>` ，从中可以获取 :ref:`flat-format <signature>` 的签名信息。

    如果 *message* 是一个字符串, 它被转换为UTF-8字节，否则使用数据用 :ref:`Arrayish <arrayish>` 表示的二进制。

.. code-block:: javascript
    :caption: *交易签名*

    let privateKey = "0x3141592653589793238462643383279502884197169399375105820974944592"
    let wallet = new ethers.Wallet(privateKey)

    console.log(wallet.address)
    // "0x7357589f8e367c2C31F51242fB77B350A11830F3"

    // All properties are optional
    let transaction = {
        nonce: 0,
        gasLimit: 21000,
        gasPrice: utils.bigNumberify("20000000000"),

        to: "0x88a5C2d9919e46F883EB62F7b8Dd9d0CC45bc290",
        // ... or supports ENS names
        // to: "ricmoo.firefly.eth",

        value: utils.parseEther("1.0"),
        data: "0x",

        // 这可确保无法在不同网络上重复广播
        chainId: ethers.utils.getNetwork('homestead').chainId
    }

    let signPromise = wallet.sign(transaction)

    signPromise.then((signedTransaction) => {

        console.log(signedTransaction);
        // "0xf86c808504a817c8008252089488a5c2d9919e46f883eb62f7b8dd9d0cc45bc2
        //    90880de0b6b3a76400008025a05e766fa4bbb395108dc250ec66c2f88355d240
        //    acdc47ab5dfaad46bcf63f2a34a05b2cb6290fd8ff801d07f6767df63c1c3da7
        //    a7b83b53cd6cea3d3075ef9597d5"

        // 现在可以将其发送到以太坊网络
        let provider = ethers.getDefaultProvider()
        provider.sendTransaction(signedTransaction).then((tx) => {

            console.log(tx);
            // {
            //    // These will match the above values (excluded properties are zero)
            //    "nonce", "gasLimit", "gasPrice", "to", "value", "data", "chainId"
            //
            //    // These will now be present
            //    "from", "hash", "r", "s", "v"
            //  }
            // Hash:
        });
    })


.. code-block:: javascript
    :caption: *签名文本消息*

    let privateKey = "0x3141592653589793238462643383279502884197169399375105820974944592"
    let wallet = new ethers.Wallet(privateKey);

    // 签名文本消息
    let signPromise = wallet.signMessage("Hello World!")

    signPromise.then((signature) => {

        // Flat-format
        console.log(signature);
        // "0xea09d6e94e52b48489bd66754c9c02a772f029d4a2f136bba9917ab3042a0474
        //    301198d8c2afb71351753436b7e5a420745fed77b6c3089bbcca64113575ec3c
        //    1c"

        // Expanded-format
        console.log(ethers.utils.splitSignature(signature));
        // {
        //   r: "0xea09d6e94e52b48489bd66754c9c02a772f029d4a2f136bba9917ab3042a0474",
        //   s: "0x301198d8c2afb71351753436b7e5a420745fed77b6c3089bbcca64113575ec3c",
        //   v: 28,
        //   recoveryParam: 1
        //  }
    });

.. code-block:: javascript
    :caption: *签名二进制信息*

    let privateKey = "0x3141592653589793238462643383279502884197169399375105820974944592"
    let wallet = new ethers.Wallet(privateKey);

    // The 66 character hex string MUST be converted to a 32-byte array first!
    let hash = "0x3ea2f1d0abf3fc66cf29eebb70cbd4e7fe762ef8a09bcc06c8edf641230afec0";
    let binaryData = ethers.utils.arrayify(hash);

    let signPromise = wallet.signMessage(binaryData)

    signPromise.then((signature) => {

        console.log(signature);
        // "0x5e9b7a7bd77ac21372939d386342ae58081a33bf53479152c87c1e787c27d06b
        //    118d3eccff0ace49891e192049e16b5210047068384772ba1fdb33bbcba58039
        //    1c"
    });

-----

与链交互
---------------------

这些操作要求钱包关联了一个  |provider| 。

:sup:`prototype` . getBalance ( [ blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    返回一个包含钱包余额的 a :ref:`Promise <promise>` 。
    (余额为 :ref:`BigNumber <bignumber>`, 单位 **wei**) ，可选参数 :ref:`blockTag <blocktag>`.

:sup:`prototype` . getTransactionCount ( [ blockTag :sup:`= "latest"` ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<number>`
    返回一个包含账号已经发送交易数量（同样称为 *nonce*）的 :ref:`Promise <promise>` ，可选参数 :ref:`blockTag <blocktag>` 。


:sup:`prototype` . estimateGas ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<BigNumber>`
    返回给定**交易**需要的（测算）Gas，返回 :ref:`BigNumber <bignumber>` 的 a :ref:`Promise <promise>` 。

.. _sendTransaction:

:sup:`prototype` . sendTransaction ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    发送 *交易* (参考 :ref:`Transaction Requests <transaction-request>`) 到网络，返回一个 可以获取  :ref:`Transaction Response <transaction-response>` 的 :ref:`Promise <promise>` 
    任何没有提供的属性将从网络获取填充。

.. code-block:: javascript
    :caption: *查询网络*

    // We require a provider to query the network
    let provider = ethers.getDefaultProvider();

    let privateKey = "0x3141592653589793238462643383279502884197169399375105820974944592"
    let wallet = new ethers.Wallet(privateKey, provider);

    let balancePromise = wallet.getBalance();

    balancePromise.then((balance) => {
        console.log(balance);
    });

    let transactionCountPromise = wallet.getTransactionCount();

    transactionCountPromise.then((transactionCount) => {
        console.log(transactionCount);
    });


.. code-block:: javascript
    :caption: *发送以太*

    // We require a provider to send transactions
    let provider = ethers.getDefaultProvider();

    let privateKey = "0x3141592653589793238462643383279502884197169399375105820974944592"
    let wallet = new ethers.Wallet(privateKey, provider);

    let amount = ethers.utils.parseEther('1.0');

    let tx = {
        to: "0x88a5c2d9919e46f883eb62f7b8dd9d0cc45bc290",
        // ... or supports ENS names
        // to: "ricmoo.firefly.eth",

        // We must pass in the amount as wei (1 ether = 1e18 wei), so we
        // use this convenience function to convert ether to wei.
        value: ethers.utils.parseEther('1.0')
    };

    let sendPromise = wallet.sendTransaction(tx);

    sendPromise.then((tx) => {
        console.log(tx);
        // {
        //    // All transaction fields will be present
        //    "nonce", "gasLimit", "pasPrice", "to", "value", "data",
        //    "from", "hash", "r", "s", "v"
        // }
    });


-----

处理加密的 JSON 钱包文件
--------------------------------------------

很多系统以各种格式将私钥存储为加密的JSON钱包文件（keystore）。 keystore有好几个使用的格式和算法，ethers.js 都能够支持。
当能只有经过正确安全的密码验证才可以生成keystore 或 解密出 Wallet 对象。

.. note::

    关于加密的JSON钱包文件（keystore），可参考阅读 ` 钱包开发之 - 账号 Keystore 文件导入导出 <https://learnblockchain.cn/2018/10/25/eth-web-wallet_2/>`_ 。


从JSON钱包文件（keystore）创建 Wallet 对象，  参考 :ref:`Wallet.fromEncryptedJson <fromEncryptedJson>` 。

:sup:`prototype` . encrypt ( password [ , options [ , progressCallback ] ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<string>`
    使用*密码password* 加密钱包生成加密的JSON钱包文件（keystore）。

    参数 options 是可选的，有效选项如下：

        - **salt** --- scrypt （一个秘钥衍生算法） 的盐
        - **iv** --- aes-ctr-128 需要使用的初始化矢量
        - **uuid** --- 钱包要用的 UUID 
        - **scrypt** --- scrypt 算法的参数 (N, r 及 p)
        - **entropy** --- 通常不指定，钱包的助记词熵;
        - **mnemonic** --- 通常不指定，钱包的助记词
        - **path** --- t通常不指定，钱包的助记词路径

    如果使用了参数 *progressCallback* ,  它将在加密期间周期的调用参数指定的函数，Callback 函数需要制定一个参数，用来接收进度（介于0和1之间，包括0和1）。

.. code-block:: javascript
    :caption: *加密钱包输出 JSON wallet*

    let password = "password123";

    function callback(progress) {
        console.log("Encrypting: " + parseInt(progress * 100) + "% complete");
    }

    let encryptPromise = wallet.encrypt(password, callback);

    encryptPromise.then(function(json) {
        console.log(json);
    });

-----

.. _signer:

|signer| 接口
==================

Signer API 是一个抽象类，当需要 |signer| 时就可以扩展实现它（不过是本库还是其他的库）。

 :ref:`Wallet <wallet>` 就是 |signer| 的一个继承实，以及 :ref:`JsonRpcSigner <signer-jsonrpc>` 和 `Ledger Hardware Wallet Signer`_ 。

为了实现一个 |signer| , 需要继承抽象类 *ethers.types.Signer* 并实现下面的属性：

:sup:`object` . provider
    （可选）连接网络的 :ref:`Provider <api-provider>` 。 不过，如果没有*提供者*，**只有** *write-only* 操作可以工作。

:sup:`object` . getAddress ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Address>`
    返回可获取账号地址的 :ref:`Promise <promise>` 。

:sup:`object` . signMessage ( message ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    返回包含*信息message* :ref:`Flat-Format Signature <signature>` 的 :ref:`Promise <promise>` 。

    如果参数 *message* 是字符串, 它被转换为UTF-8字节，否则使用数据用 :ref:`Arrayish <arrayish>` 表示的二进制。

:sup:`object` . sendTransaction ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    发送 *交易transaction* (参考 :ref:`Transaction Requests <transaction-request>`) 到网络，返回一个 可以获取  :ref:`Transaction Response <transaction-response>` 的 :ref:`Promise <promise>` 
    任何没有提供的属性将从网络获取填充。

-----

.. _BIP-039: https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki
.. _BIP-044: https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki
.. _Ledger Hardware Wallet Signer: https://github.com/ethers-io/ethers-ledger
.. _JSON 钱包文件: https://medium.com/@julien.maffre/what-is-an-ethereum-keystore-file-86c8c5917b97
.. EOF
