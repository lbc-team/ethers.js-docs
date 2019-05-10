.. include:: glossaries.rst
.. |nbsp| unicode:: U+00A0 .. non-breaking space

.. _api-wallet:

在使用接口之前，需要先确保正确 `引入了 ethers.js <getting-started>`_ 。
 

|wallet| 类 和 |signer| 接口
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
    Returns a :ref:`Promise <promise>` with the estimated cost for *transaction* (as a
    :ref:`BigNumber <bignumber>`, in **gas**)

.. _sendTransaction:

:sup:`prototype` . sendTransaction ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    Sends the *transaction* (see :ref:`Transaction Requests <transaction-request>`) to
    the network and returns a :ref:`Promise <promise>` that resolves to a
    :ref:`Transaction Response <transaction-response>`. Any properties that are not
    provided will be populated from the network.

.. code-block:: javascript
    :caption: *query the network*

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

Many systems store private keys as encrypted JSON wallets (keystore), in various formats. There are several
formats and algorithms that are used, all of which are supported to be read.
Only the secure scrypt variation can be generated.

See :ref:`Wallet.fromEncryptedJson <fromEncryptedJson>` for creating a
Wallet instance from a JSON wallet.

:sup:`prototype` . encrypt ( password [ , options [ , progressCallback ] ] ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<string>`
    Encrypts the wallet as an encrypted JSON wallet, with the *password*.

    All options are optional. The valid options are:

        - **salt** --- the salt to use for scrypt
        - **iv** --- the initialization vecotr to use for aes-ctr-128
        - **uuid** --- the UUID to use for the wallet
        - **scrypt** --- the scrypt parameters to use (N, r and p)
        - **entropy** --- the mnemonic entropy of this wallet; generally you should **not** specify this
        - **mnemonic** --- the mnemonic phrase of this wallet; generally you should **not** specify this
        - **path** --- the mnemonic path of this wallet; generally you should **not** specify this

    If the *progressCallback* is specified, it will be called periodically during
    encryption with a value between 0 and 1, inclusive indicating the progress.


.. code-block:: javascript
    :caption: *encrypt a wallet as an encrypted JSON wallet*

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

The Signer API is an abstract class which makes it easy to extend and add new signers,
that can be used by this library and extension libraries. The :ref:`Wallet <wallet>`
extends the Signer API, as do the :ref:`JsonRpcSigner <signer-jsonrpc>` and the
`Ledger Hardware Wallet Signer`_.

To implement a Signer, inherit the abstract class *ethers.types.Signer* and implement
the following properties:

:sup:`object` . provider
    A :ref:`Provider <api-provider>` that is connected to the network. This is optional, however,
    without a *provider*, **only** *write-only* operations should be expected to work.

:sup:`object` . getAddress ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Address>`
    Returns a :ref:`Promise <promise>` that resolves to the account address.

:sup:`object` . signMessage ( message ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<hex>`
    Returns a :ref:`Promise <promise>` that resolves to the :ref:`Flat-Format Signature <signature>`
    for the *message*.

    If *message* is a string, it is converted to UTF-8 bytes, otherwise it is
    preserved as a binary representation of the :ref:`Arrayish <arrayish>` data.

:sup:`object` . sendTransaction ( transaction ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<TransactionResponse>`
    Sends the *transaction* (see :ref:`Transaction Requests <transaction-request>`) to
    the network and returns a :ref:`Promise <promise>` that resolves to a
    :ref:`Transaction Response <transaction-response>`. Any properties that are not
    provided will be populated from the network.

-----

.. _BIP-039: https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki
.. _BIP-044: https://github.com/bitcoin/bips/blob/master/bip-0044.mediawiki
.. _Ledger Hardware Wallet Signer: https://github.com/ethers-io/ethers-ledger
.. _JSON 钱包文件: https://medium.com/@julien.maffre/what-is-an-ethereum-keystore-file-86c8c5917b97
.. EOF
