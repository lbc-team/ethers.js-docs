开始使用
***************

ethers.js 库是为以太坊 提供的一个的小而完整的 JavaScript 库。

-----

在 Node.js 项目中安装
==========================

项目目录下安装 ethers.js 库::

    /home/ricmoo/my-project> npm install --save ethers

导入ethers
------------------

.. code-block:: javascript
    :caption: *JavaScript (ES3)*

    var ethers = require('ethers');

.. code-block:: javascript
    :caption: *JavaScript (ES5 or ES6)*

    const ethers = require('ethers');

.. code-block:: javascript
    :caption: *JavaScript (ES6) / TypeScript*

    import { ethers } from 'ethers';


-----

在Web应用中引入ethers
=============================

出于安全考虑，通常最好复制一份 `ethers-v4.min.js`_  到自己的应用程序服务器，如果快速原型体验，使用Ethers CDN应该足够了。

.. code-block:: html
    :caption: *HTML*

    <!-- 会导出一个全局的变量: ethers -->
    <script src="https://cdn.ethers.io/scripts/ethers-v4.min.js"
            charset="utf-8"
            type="text/javascript">
    </script>


-----

.. _npm is installed: https://nodejs.org/en/
.. _ethers-v4.min.js: https://cdn.ethers.io/scripts/ethers-v4.min.js
