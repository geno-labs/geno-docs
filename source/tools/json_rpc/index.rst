##############################################################
RPC 接口说明
##############################################################

JSON-RPC 简介
==============

JSON-RPC 是区块链系统与外部应用程序交互的标准协议。它通过定义一组远程过程调用（RPC）方法，使得客户端可以与区块链节点进行通信。JSON-RPC 通常使用 HTTP 或 WebSocket 作为传输协议，并使用 JSON 格式来编码请求和响应。

为什么使用 JSON-RPC？
---------------------

- **标准化**：JSON-RPC 提供了一种标准化的方式来访问区块链数据和功能。
- **简单性**：JSON 数据格式易于理解和处理。
- **跨平台**：支持多种编程语言和平台。

Error Code
==============



通用状态码
------------------

- **Success : 0**

   请求成功

- **InternalError : -1**
   
   未知错误

-1xx 接口请求类状态码
------------------

- **InvalidReq : -100**

   无效的客户端请求

- **FunctonNotFound : -101**

   无效的 json-rpc 请求,API接口不存在

- **InvalidArgs : -103**

   无效的请求参数

- **InvalidFormat : -104**

   无效的请求格式



-2xx 交易类状态码
------------------

- **TxSetInvalidNonce : -200**

   交易 Nonce 值错误

- **TxSetIsFull : -201**

   达到交易池容量上限

- **TxSetTooManyTransactions : -202**

   交易池每个账户达到容量上限

- **TxSetInvalidUpdate : -203**
   
   交易更新失败

- **TxSetValidationError : -204**
   
   交易验证失败

- **TxSetIsPending : -205**
   
   交易已经在交易池排队

- **TxSetUnknownError : -206**
   
   交易池未知错误


JSON-RPC 方法概览
==================

.. toctree::
   :maxdepth: 1

   json_rpc_api.md

   
