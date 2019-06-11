.. include:: glossaries.rst
.. |nbsp| unicode:: U+00A0 .. non-breaking space

.. _api-contract:

合约
*********

合约是在以太坊区块链上的可执行程序的抽象。合约具有代码 (称为字节代码) 以及分配的长期存储 (storage)。每个已部署的合约都有一个地址, 用它连接到合约, 可以向其发送消息来调用合约方法。

合约可以发出 **事件**, 它可以被应用程序监听（订阅）， 当合约执行了特定操作时, 应用程序将收到通知。事件是无法在合约内读取的。

在合约上可以调用两种类型的方法：

    `视图方法 <https://learnblockchain.cn/docs/solidity/contracts.html#view>`_ : 不能添加、移除或更改存储中的任何数据，也不能记录任何事件，并且只能调用其他合约上**视图方法**。
    这些方法是免费的（不需要以太）调用。 结果也可以返回给调用者。

    **非视图方法**: 需要支付费用（用Ether），但可以执行任何所需的状态更改操作，记录事件，发送ether并在其他合约上调用非视图方法。
    这些方法**不能**将其结果返回给调用者。 这些方法必须由交易触发，由外部拥有的账户（EOA）直接或间接发送（如从另一个合约调用），并且只有在交易被打包（挖矿）后才会产生效果。
    因此，这些操作所需的持续时间可能变化很大，并且取决于交易Gas价格、网络拥塞和矿工优先选择方法。


合约 API 提供了简单的方法来连接到一个合约并调用它的方法，它作为 JavaScript 对象上的函数，处理所有的二进制协议转换，内部名称修改和主题构造。
这使得合约对象可以像任何标准的JavaScript对象一样使用，而不必担心以太坊虚拟机或区块链的低级细节。

合约Contract对象是一个元类，它是一个在运行时定义类的类。 可以提供合约定义（称为应用程序二进制接口或ABI）以及可用的方法和事件可以动态添加到对象中。

在本文档中，我们将参考以下合约。


.. code-block:: javascript
    :caption: *SimpleStorage 合约*

    pragma solidity ^0.4.24;

    contract SimpleStorage {

        event ValueChanged(address indexed author, string oldValue, string newValue);

        string _value;

        constructor(string value) public {
            emit ValueChanged(msg.sender, _value, value);
            _value = value;
        }

        function getValue() view public returns (string) {
            return _value;
        }

        function setValue(string value) public {
            emit ValueChanged(msg.sender, _value, value);
            _value = value;
        }
    }

-----

.. _contract-deployment:

部署合约
====================

为了将合约部署到以太坊网络，可以创建一个 **ContractFactory** 来管理合约字节码和 **应用程序二进制接口** （ABI），ABI通常由 *Solidity* 编译器生成。


创建 ContractFactory
---------------------------

new :sup:`ethers` . ContractFactory ( abi , bytecode [ , signer ] )
    创建合约工厂，通过部署合约的 *字节码* 以及在 *abi* 中定义的构造函数来创建合约工厂。 *签名器 singer* 将用于发送任何部署交易

:sup:`ethers` . ContractFactory . fromSolidity ( compilerOutput [ , signer ] )
    从Solidity编译器的 *compilerOutput* 或从 *Truffle*  JSON  (如： ``output.contracts['SimpleStorage.sol'].SimpleStorage``) 创建合约工厂 ContractFactory。
   

:sup:`prototype` . connect ( signer ) |nbsp| :sup:`=>` |nbsp| :sup:`ContractFactory`
    创建连接到新 *签名器signer* 的ContractFactory **新实例** 。


Prototype属性
------------------

:sup:`prototype` . bytecode
    返回合约的可执行字节码。

:sup:`prototype` . interface
    返回合约应用程序二进制接口(ABI)。

:sup:`prototype` . signer
    返回 :ref:`签名器Signer <signer>` 用来发送交易到网络。
    如果为空，则 ``deploy()``  不可以调用。


关联地址
----------

:sup:`prototype` . attach ( address ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    使用合约接口和签名器Singer 连接到现有合约实例。参数为合约 **地址**


部署合约
----------

:sup:`prototype` . deploy ( ... ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Contract>`
    创建一个部署合约的交易，并使用合约 :ref:`Signer <signer>` 将其发送到网络，并返回合约对象的  :ref:`Promise <promise>` 。 交易记录通过 contract.deployTransaction 获取。
    参数将传递给合约构造函数constructor。
    请记住，合约可能不会立即挖出。 ``contract.deployed()`` 函数将返回一个 :ref:`Promise <promise>` ，将在部署合约后获取实例，或者在部署被拒绝时生成一个错误。

:sup:`prototype` . getDeployTransaction ( ... ) |nbsp| :sup:`=>` |nbsp| :sup:`UnsignedTransaction`
    返回部署合约（提供了构造参数）的交易。 通常可用于离线签名交易或分析工具。

.. code-block:: javascript
    :caption: *部署合约*

    const ethers = require('ethers');

    // The Contract interface
    let abi = [
        "event ValueChanged(address indexed author, string oldValue, string newValue)",
        "constructor(string value)",
        "function getValue() view returns (string value)",
        "function setValue(string value)"
    ];

    // The bytecode from Solidity, compiling the above source
    let bytecode = "0x608060405234801561001057600080fd5b506040516105bd3803806105bd8339" +
                     "8101604081815282518183526000805460026000196101006001841615020190" +
                     "91160492840183905293019233927fe826f71647b8486f2bae59832124c70792" +
                     "fba044036720a54ec8dacdd5df4fcb9285919081906020820190606083019086" +
                     "9080156100cd5780601f106100a2576101008083540402835291602001916100" +
                     "cd565b820191906000526020600020905b815481529060010190602001808311" +
                     "6100b057829003601f168201915b505083810382528451815284516020918201" +
                     "9186019080838360005b838110156101015781810151838201526020016100e9" +
                     "565b50505050905090810190601f16801561012e578082038051600183602003" +
                     "6101000a031916815260200191505b5094505050505060405180910390a28051" +
                     "610150906000906020840190610157565b50506101f2565b8280546001816001" +
                     "16156101000203166002900490600052602060002090601f0160209004810192" +
                     "82601f1061019857805160ff19168380011785556101c5565b82800160010185" +
                     "5582156101c5579182015b828111156101c55782518255916020019190600101" +
                     "906101aa565b506101d19291506101d5565b5090565b6101ef91905b80821115" +
                     "6101d157600081556001016101db565b90565b6103bc806102016000396000f3" +
                     "0060806040526004361061004b5763ffffffff7c010000000000000000000000" +
                     "0000000000000000000000000000000000600035041663209652558114610050" +
                     "57806393a09352146100da575b600080fd5b34801561005c57600080fd5b5061" +
                     "0065610135565b60408051602080825283518183015283519192839290830191" +
                     "85019080838360005b8381101561009f57818101518382015260200161008756" +
                     "5b50505050905090810190601f1680156100cc57808203805160018360200361" +
                     "01000a031916815260200191505b509250505060405180910390f35b34801561" +
                     "00e657600080fd5b506040805160206004803580820135601f81018490048402" +
                     "8501840190955284845261013394369492936024939284019190819084018382" +
                     "80828437509497506101cc9650505050505050565b005b600080546040805160" +
                     "20601f6002600019610100600188161502019095169490940493840181900481" +
                     "0282018101909252828152606093909290918301828280156101c15780601f10" +
                     "610196576101008083540402835291602001916101c1565b8201919060005260" +
                     "20600020905b8154815290600101906020018083116101a457829003601f1682" +
                     "01915b505050505090505b90565b604080518181526000805460026000196101" +
                     "00600184161502019091160492820183905233927fe826f71647b8486f2bae59" +
                     "832124c70792fba044036720a54ec8dacdd5df4fcb9285918190602082019060" +
                     "60830190869080156102715780601f1061024657610100808354040283529160" +
                     "200191610271565b820191906000526020600020905b81548152906001019060" +
                     "200180831161025457829003601f168201915b50508381038252845181528451" +
                     "60209182019186019080838360005b838110156102a557818101518382015260" +
                     "200161028d565b50505050905090810190601f1680156102d257808203805160" +
                     "01836020036101000a031916815260200191505b509450505050506040518091" +
                     "0390a280516102f49060009060208401906102f8565b5050565b828054600181" +
                     "600116156101000203166002900490600052602060002090601f016020900481" +
                     "019282601f1061033957805160ff1916838001178555610366565b8280016001" +
                     "0185558215610366579182015b82811115610366578251825591602001919060" +
                     "01019061034b565b50610372929150610376565b5090565b6101c991905b8082" +
                     "1115610372576000815560010161037c5600a165627a7a723058202225a35c50" +
                     "7b31ac6df494f4be31057c7202b5084c592bdb9b29f232407abeac0029";


    // 连接网络
    let provider = ethers.getDefaultProvider('ropsten');

    // 加载钱包以部署合约
    let privateKey = '0x0123456789012345678901234567890123456789012345678901234567890123';
    let wallet = new ethers.Wallet(privateKey, provider);

    // 部署是异步的，所以我们使用异步IIFE
    (async function() {

        // 常见合约工厂实例
        let factory = new ethers.ContractFactory(abi, bytecode, wallet);

        // 请注意，我们将 "Hello World" 作为参数传递给合约构造函数constructor
        let contract = await factory.deploy("Hello World");

        // 部署交易有一旦挖出，合约地址就可用
        // 参考: https://ropsten.etherscan.io/address/0x2bd9aaa2953f988153c8629926d22a6a5f69b14e
        console.log(contract.address);
        // "0x2bD9aAa2953F988153c8629926D22A6a5F69b14E"

        // 发送到网络用来部署合约的交易
        // 查看: https://ropsten.etherscan.io/tx/0x159b76843662a15bd67e482dcfbee55e8e44efad26c5a614245e12a00d4b1a51
        console.log(contract.deployTransaction.hash);
        // "0x159b76843662a15bd67e482dcfbee55e8e44efad26c5a614245e12a00d4b1a51"

        //合约还没有部署;我们必须等到它被挖出
        await contract.deployed()

        // 好了 合约已部署。
    })();


-----

连接已有合约
=================================

一旦合约被部署，就可以连接它，使用 **Contract** 对象。

Once a Contract has been deployed, it can be connected to using
the **Contract** object.

连接合约
------------------------

new :sup:`ethers` . Contract ( addressOrName , abi , providerOrSigner )
    通过 **abi** 及 **addressOrName** 连接合约，连接作为 **providerOrSigner**。

    有关 **abi** 支持的格式，请参阅 :ref:`合约 ABI <contract-abi>` 。

    有关访问能力和限制，请参阅 :ref:`提供者与签名器 <providers-vs-signers>`

.. code-block:: javascript
    :caption: *连接已有合约*

    const ethers = require('ethers');

    // The Contract interface
    let abi = [
        "event ValueChanged(address indexed author, string oldValue, string newValue)",
        "constructor(string value)",
        "function getValue() view returns (string value)",
        "function setValue(string value)"
    ];

    // Connect to the network
    let provider = ethers.getDefaultProvider();

    // 地址来自上面部署的合约
    let contractAddress = "0x2bD9aAa2953F988153c8629926D22A6a5F69b14E";

    // 使用Provider 连接合约，将只有对合约的可读权限
    let contract = new ethers.Contract(contractAddress, abi, provider);


.. code-block:: javascript
    :caption: *调用只读的视图方法*

    // 获取当前的值
    let currentValue = await contract.getValue();

    console.log(currentValue);
    // "Hello World"

.. code-block:: javascript
    :caption: *调用非视图方法方法*

    // 从私钥获取一个签名器 Signer 
    let privateKey = '0x0123456789012345678901234567890123456789012345678901234567890123';
    let wallet = new ethers.Wallet(privateKey, provider);

    // 使用签名器创建一个新的合约实例，它允许使用可更新状态的方法
    let contractWithSigner = contract.connect(wallet);
    // ... 或 ...
    // let contractWithSigner = new Contract(contractAddress, abi, wallet)

    // 设置一个新值，返回交易
    let tx = await contractWithSigner.setValue("I like turtles.");

    // 查看: https://ropsten.etherscan.io/tx/0xaf0068dcf728afa5accd02172867627da4e6f946dfb8174a7be31f01b11d5364
    console.log(tx.hash);
    // "0xaf0068dcf728afa5accd02172867627da4e6f946dfb8174a7be31f01b11d5364"

    // 操作还没完成，需要等待挖矿
    await tx.wait();

    // 再次调用合约的 getValue()
    let newValue = await contract.getValue();

    console.log(currentValue);
    // "I like turtles."

.. code-block:: javascript
    :caption: *监听事件Event*

    contract.on("ValueChanged", (author, oldValue, newValue, event) => {
        // 在值变化的时候被调用

        console.log(author);
        // "0x14791697260E4c9A71f18484C9f997B308e59325"

        console.log(oldValue);
        // "Hello World"

        console.log(newValue);
        // "Ilike turtles."

        // 查看后面的事件触发器  Event Emitter 了解事件对象的属性
        console.log(event.blockNumber);
        // 4115004
    });

.. code-block:: javascript
    :caption: *过滤事件Events*

    // 使用签名器地址作为事件触发者进行过滤
    let filter = contract.filters.ValueChanged(wallet.address);

    contract.on(filter, (author, oldValue, newValue, event) => {
        // 只有当我们的账号（签名器地址）更改的数据才回调
    });

-----

Prototype 属性
---------------

:sup:`prototype` . address
    返回合约地址或 ENS 名称。

:sup:`prototype` . deployTransaction
    如果合约通过ContractFactory部署，返回部署的交易，否则为null 。

:sup:`prototype` . interface
    解析的ABI的 :ref:`接口 <api-interface>` 元类，通常不需要直接访问。

根据提供的ABI，附加属性将在运行时添加到原型 Prototype 中，请参阅:ref:`合约元类 Meta-Class <contract-metaclass>` 。

-----

等待部署
----------------------

:sup:`prototype` . deployed ( ) |nbsp| :sup:`=>` |nbsp| :sup:`Promise<Contract>`
    如果该合约是通过 ``deploy()`` 产生，则返回一个 :ref:`Promise <promise>` ，一旦部署被挖掘，从 :ref:`Promise <promise>` 可以获得该合约，如果该合约未能部署，则无法获取。
    如果是已经部署的合约，一旦链上的代码被确认，:ref:`Promise <promise>` 就会resolve 。

-----

.. _contract-metaclass:

元类 Meta-Class 属性
=========================

Since a Contract is dynamic and loaded at run-time, many of the properties
that will exist on a Contract are determined at run-time from
the :ref:`Contract ABI <contract-abi>`.

合约方法
----------------

All functions populated from the ABI are also included on the contract object
directly, for example ``contract.functions.getValue()`` can also be called
using ``contract.getValue()``.

:sup:`prototype` . functions . *functionName*
    An object that maps each ABI function name to a function that will
    either call (for constant functions) or sign and send a transaction
    (for non-constant functions)

    Calling a **Constant** function requires either a :ref:`Provider <provider-connect>` or
    a Signer with a :ref:`Provider <provider-connect>`.

    Calling a **Non-Constant** function (i.e. sending a transaction) requires a
    :ref:`Signer <signer>`.

:sup:`prototype` . estimate . *functionName*
    An object that maps each ABI function name to a function that will
    estimate the cost the provided parameters.


合约事件过滤器
----------------------

Filters allow for a flexible and efficient way to fetch only a subset of the
events that match specific criteria. The ``filters`` property contains a
function for every Event in the ABI that computes a Filter for a given
set of values. The ``null`` matches any value.

:sup:`prototype` . filters . *eventname*
    A function that generates filters that can be listened to, using the
    ``on(eventName, ...)`` function, filtered by the Event values.

.. code-block:: javascript
    :caption: *Filtering Events*

    // A filter from me to anyone
    let filterFromMe = contract.filters.Transfer(myAddress);

    // A filter from anyone to me
    let filterToMe = contract.filters.Transfer(null, myAddress);

    // A filter from me AND to me
    let filterFromMeToMe = contract.filters.Transfer(myAddress, myAddress);

    contract.on(filterFromMe, (fromAddress, toAddress, value, event) => {
        console.log('I sent', value);
    });

    contract.on(filterToMe, (fromAddress, toAddress, value, event) => {
        console.log('I received', value);
    });

    contract.on(filterFromMeToMe, (fromAddress, toAddress, value, event) => {
        console.log('Myself to me', value);
    });


-----

.. _contract-overrides:

override 指定交易附加信息
==============================

Every Contract method may take one additional (optional) parameter which specifies the
transaction (or call) overrides.

.. code-block:: javascript
    :caption: *Contract Transaction Overrides*

    // All overrides are optional
    let overrides = {

        // The maximum units of gas for the transaction to use
        gasLimit: 23000,

        // The price (in wei) per unit of gas
        gasPrice: utils.parseUnits('9.0', 'gwei'),

        // The nonce to use in the transaction
        nonce: 123,

        // The amount to send with the transaction (i.e. msg.value)
        value: utils.parseEther('1.0'),

        // The chain ID (or network ID) to use
        chainId: 1

    };

    // Solidity: function someFunction(address addr) public
    let tx = contract.someFunction(addr, overrides)

.. code-block:: javascript
    :caption: *Contract Call Overrides*

    let overrides = {

        // The address to execute the call as
        from: "0x0123456789012345678901234567890123456789",

        // The maximum units of gas for the transaction to use
        gasLimit: 23000,

    };

    // Solidity: function someFunction(address addr) public pure returns (bytes32 result)
    let result = contract.someFunction(addr, overrides)

-----

.. _contract-event-emitter:

事件触发器Event Emitter
=========================

Each Contract supports many of the operations available from the `Event Emitter API`_.

To listen for Events, the contract requires either a :ref:`Provider <provider-connect>` or
a Signer with a :ref:`Provider <provider-connect>`.


Event Names
-----------

The available eventNames are:

    - **string** -- The name of an event (e.g. "TestEvent" or "TestEvent(string, uint)")
    - **filter** -- See :ref:`Contract Filters <contract-filter>`
    - **\*** -- All events


Event Object
------------

All event callbacks receive the parameters specified in the ABI as well as one additional
Event Object with

    - **blockNumber**, **blockHash**, **transactionHash** -- The Block and Transaction of the Log
    - **address** -- The contract address for the Log
    - **data** -- The Log data
    - **topics** -- An array of the Log topics
    - **args** -- An array of the parsed  arguments for the event
    - **event** -- the name of the event (e.g. "Transfer")
    - **eventSignature** -- the full signature of the event (e.g. "Transfer(address,address,uint256)")
    - **getBlock()** -- A function that resolves to the Block containing the Log
    - **getTransaction()** -- A function that resolves to the Transaction containing the Log
    - **getTransactionReceipt()** -- A function that resolves to the Transaction Receipt containing the Log
    - **removeListener()** -- A function that removes this callack as a listener
    - **decode(data, topics)** -- A function that decodes data and topics into parsed arguments


Configuring Events
------------------

:sup:`prototype` . on ( eventName , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    Registers *callback* to be called on every *eventName*. Returns the contract, so calls may be chained.

:sup:`prototype` . addEventListner ( eventName , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    An alias for ``on``.

:sup:`prototype` . once ( eventName , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    Register *callback* to be called at most once, for *eventName*. Returns the contract, so calls may be chained.

:sup:`prototype` . emit ( eventName , ... ) |nbsp| :sup:`=>` |nbsp| :sup:`boolean`
    Trigger all callbacks for *eventName*, returning true if there was at
    least one listener. This should generally not be called directly.

:sup:`prototype` . listenerCount ( [ eventName ] ) |nbsp| :sup:`=>` |nbsp| :sup:`number`
    Returns the number of callbacks registered for *eventName*.

:sup:`prototype` . listeners ( eventName ) |nbsp| :sup:`=>` |nbsp| :sup:`Listeners[]`
    Returns a list of callbacks for *eventName*.

:sup:`prototype` . removeAllListeners ( eventName ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    De-registers all listeners for *eventName*. Returns the contract, so calls may be chained.

:sup:`prototype` . removeListener ( eventName , callback ) |nbsp| :sup:`=>` |nbsp| :sup:`Contract`
    De-registers the specific *callback* for *eventName*. Returns the contract, so calls may be chained.

.. code-block:: javascript
    :caption: *Events*

    contract.on("ValueChanged", (oldValue, newValue, event) => {
        console.log(oldValue, newValue);
    });


-----

.. _providers-vs-signers:

用 Providers 还是 Signers
===========================

A Contract object has a notion of an "frame of reference", which will determine
what type of access and whom the Contract is enacted upon as. This is specified
by the **providerOrSigner** parameter when connecting to a Contract.

There are three possible cases for connecting a Contract using the providerOrSigner.

============================================ ========================================
providerOrSigner                             Operation Privileges
============================================ ========================================
:ref:`Provider <provider-connect>`           Read-Only Access
:ref:`Signer <signer>` (without a provider)  Write-Only Access (as account owner)
:ref:`Signer <signer>` (with a provider)     Read and Write Access (as account owner)
============================================ ========================================

The **providerOrSigner** is immutable, so to change the "frame of reference" to
another account or provider, use the ``connect`` function.

:sup:`prototype` . connect ( providerOrSigner )
    Create a **new instance** of the Contract object connected as *providerOrSigner*.


类型在合约与 JavaScript 之间的转换
========================================

There are many variable types available in *Solidity*, some which convert
to and from JavaScript gracefully, and others that do not. Here are some
note regarding passing and returning values in Contracts.


Bytes
-----

Bytes are available in fixed-length or dynamic-length variants. In both cases, the
values are returned as a hex string and may be passed in as either a hex string or
as an :ref:`arrayish <arrayish>`.

To convert the string into an array, use the :ref:`arrayify() <arrayish>` utility function.


Integers
--------

Integers in *solidity* are a fixed number of bits (aligned to the nearest byte)
and are available in signed and unsigned variants.

For example, a **uint256** is 256 bits (32 bytes) and unsigned. An **int8**
is 8 bits (1 byte) and signed.

When the type is 48 bits (6 bytes) or less, values are returned as a JavaScript
Number, since Javascript Numbers are safe to use up to 53 bits.

Any types with 56 bits (7 bytes) or more will be returned as a BigNumber,
even if the *value* is within the 53 bit safe range.

When passing numeric values in, JavaScript Numbers, hex strings or any BigNumber
is acceptable (however, take care when using JavaScript Numbers and performing
mathematical operations on them).

The **uint** and **int** types are aliases for **uint256** and **int256**,
respectively.


Strings
-------

For short strings, many Contracts use a bytes32 to encode a null-terminated
string representation, rather than a length-prefixed representation, so the
:ref:`formatBytes32String <bytes32string>` and :ref:`parseBytes32String <bytes32string>`
utility functions can be used to handle this conversion.

To convert between the two dynamic types, strings and bytes, the
:ref:`toUtf8Bytes() <utf8-strings>` and :ref:`toUtf8String() <utf8-strings>`
utility functions can be used.


Structs
-------

Structs can be specified as Objects with their named properties, or as an Array,
the same length as the struct.

**Constant** methods which return a single item, return that item directly. If the
method returns multiple values then an object is returned which can be accessed by
either the named properties or by their indices, in which case both point to the
**same instance**.


.. code-block:: javascript
    :caption: *Example Return Types*

    /**
     *  Contract Methods
     *
     *  function oneItem() public view returns (uint256 param1);
     *  function twoItems() public view returns (uint256 param1, uint256 param2);
     *
     */

     let resultOne = await oneItem();
     console.log(resultOne);
     // 1337

     let resultTwo = await twoItems();
     console.log(resultTwo);
     // {
     //    "param1": 1337,
     //    "param2": 42,
     //    0: 1337,
     //    1: 42,
     //    length: 2
     // }

     assert.ok(resultTwo[0] === resultTwo.param1);
     assert.ok(resultTwo[1] === resultTwo.param2);


-----

.. _contract-filter:

事件过滤
================

On every contract, there is a ``filters`` property, which can be used to
generate an event filter. And event filter can be passed into the ``on(eventName)``
of a contract.

.. code-block:: javascript
    :caption: *Find all ERC-20 transfers to myAddress*

    // The null field indicates any value matches, this specifies
    // "any Transfer from any to myAddress"
    let filter = contract.Transfer(null, myAddress);

    // Listen for our filtered results
    contract.on(filter, (from, to, value) => {
        console.log('I received ' + value.toString() + ' tokens from ' + from);
    });

-----

.. _contract-abi:

|ABI|
==================================

Each Contract has a description of its interface, which describes each function
and event.

The Solidity compiler generates the ABI in a JSON format, which can be used as
a JSON string or parsed as a JavaScript Object. This is generated by the
compiler and can be loaded as a file, or copied into the source code.

The ABI may also be specified using `Human-Readable ABI`_, which is much easier
to use when typing in an ABI by hand, for example, as well as easier to read. This
is simply an array of strings, each of which is the Solidity function or event 
signature.

.. code-block:: javascript
    :caption: *Human-Readable ABI*

    let ABI = [
        "event Transfer(address from, address to, uint amount)",
        "function transfer(address to, uint amount)",
        "function symbol() view returns (string)"
    ]


-----

.. _Human-Readable ABI: https://blog.ricmoo.com/human-readable-contract-abis-in-ethers-js-141902f4d917
.. _Event Emitter API: https://nodejs.org/api/events.html#events_class_eventemitter

.. EOF
