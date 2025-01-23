# Python 测试脚本组件

区块链技术依赖于其健壮性和安全性，因此测试在开发过程中至关重要。Python 因其强大的生态系统和易用性，成为区块链测试脚本编写的热门选择。本文档旨在介绍如何使用 Python 来编写和使用区块链的测试脚本。

## 环境要求

- Python：Python 3.9 +。
- Protobuf：3.12.4

## 安装说明

首先，确保安装了必要的Python库。您可以通过以下命令进行安装：

```
pip install hashlib
pip install binascii
pip install json

pip install cryptography
pip install protobuf
```

## 核心代码

以下是区块链python测试脚本核心代码：

```
import binascii
import hashlib
import json

from cryptography.hazmat.primitives.asymmetric.ed25519 import Ed25519PrivateKey
from google.protobuf import json_format
from loguru import logger
import requests

import block_pb2
from configs import config

class BaseApi:
    
    def __init__(self):
        self.headers = {
            "Content-Type": "application/json"
        }
        self.data = {
            "jsonrpc": "2.0",
            "id": 1
        }

    def request_info(self, data):
        response = requests.post(config.url, headers=self.headers, data=json.dumps(data))
        result = response.json()
        logger.debug('\n config.Url：' + url + '\n请求：' + json.dumps(data, indent=4, ensure_ascii=False) + '\n结果：' +json.dumps(result, indent=4, ensure_ascii=False))
        return result

    def send_request(self, method, *args):
        data = {
            'method': method,
            'params': args,
        }
        data.update(self.data)
        return self.request_info(data)

    def getAccount(self, address):
        result = self.send_request('get_account', address)
        if 'result' in result:
            return result['result']
        else:
            return result

    def getNonce(self, address):
        result = self.send_request('get_nonce', address)
        if 'result' in result:
            return result['result']
        else:
            return result

    def send_raw_tx(self, sign_data):
        result = self.send_request('send_raw_tx', sign_data)
        return result

    # 组装交易结构
    def build_operation(self, sourceAddress, nonce, type, chain_id, gas_limit, gas_price):
        tx = block_pb2.Transaction()
        tx.sender = sourceAddress
        tx.nonce = nonce
        tx.chain_id = chain_id
        tx.kind = type
        tx.gas_limit = gas_limit
        tx.gas_price = gas_price
        return tx

    def oper_transfer(self, sourceAddress, nonce, type, destAddress, balance, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.transfer.to = destAddress
        tx.payload.transfer.balance = balance
        return tx

    def oper_call(self, sourceAddress, nonce, type, destAddress, balance, ctype, name, code, function, parameter, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.call.to = destAddress
        tx.payload.call.balance = balance
        tx.payload.call.ctype = ctype
        tx.payload.call.input.name = name
        tx.payload.call.input.code = code
        tx.payload.call.input.function = function
        tx.payload.call.input.parameter = parameter
        return tx

    def oper_syscall(self, sourceAddress, nonce, type, destAddress, balance, ctype, name, code, function, parameter, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.sys_call.to = destAddress
        tx.payload.sys_call.balance = balance
        tx.payload.sys_call.ctype = ctype
        tx.payload.sys_call.input.name = name
        tx.payload.sys_call.input.code = code
        tx.payload.sys_call.input.function = function
        tx.payload.sys_call.input.parameter = parameter
        return tx

    def oper_set_storage(self, sourceAddress, nonce, type, key, value, delete, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.set_storage.key = key
        tx.payload.set_storage.value = value
        tx.payload.set_storage.delete = delete
        return tx

    def oper_set_archive(self, sourceAddress, nonce, type, key, value, delete, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.set_archive.key = key
        tx.payload.set_archive.value = value
        tx.payload.set_archive.delete = delete
        return tx

    def oper_issue_asset(self, sourceAddress, nonce, type, code, amount, flags, to, metadata, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.issue_asset.code = code
        tx.payload.issue_asset.amount = amount
        tx.payload.issue_asset.flags = flags
        tx.payload.issue_asset.to = to
        tx.payload.issue_asset.metadata = metadata
        return tx

    def oper_pay_asset(self, sourceAddress, nonce, type, to, code, pay_kind, amount, authorizer, chain_id, gas_limit, gas_price):
        tx = self.build_operation(sourceAddress, nonce, type, chain_id, gas_limit, gas_price)
        tx.payload.pay_asset.to = to
        tx.payload.pay_asset.code = code
        tx.payload.pay_asset.pay_kind = pay_kind
        tx.payload.pay_asset.amount = amount
        tx.payload.pay_asset.authorizer = authorizer
        return tx

    def operation_type(self, sourceAddress, nonce, type, destAddress, balance, ctype, key, value, delete, code, amount, flags, to, metadata, kind, name, function, parameter, authorizer, chain_id, gas_limit, gas_price):
        if type == 1:
            return self.oper_transfer(sourceAddress, nonce, type, destAddress, balance, chain_id, gas_limit, gas_price)
        elif type == 2:
            return self.oper_call(sourceAddress, nonce, type, destAddress, balance, ctype, name, code, function, parameter, chain_id, gas_limit, gas_price)
        elif type == 3:
            return self.oper_syscall(sourceAddress, nonce, type, destAddress, balance, ctype, name, code, function, parameter, chain_id, gas_limit, gas_price)
        elif type == 4:
            return self.oper_set_storage(sourceAddress, nonce, type, key, value, delete, chain_id, gas_limit, gas_price)
        elif type == 5:
            return self.oper_set_archive(sourceAddress, nonce, type, key, value, delete, chain_id, gas_limit, gas_price)
        elif type == 6:
            return self.oper_issue_asset(sourceAddress, nonce, type, code, amount, flags, to, metadata, chain_id, gas_limit, gas_price)
        elif type == 7:
            return self.oper_pay_asset(sourceAddress, nonce, type, destAddress, code, kind, amount, authorizer, chain_id, gas_limit, gas_price)

    # 交易hash处理
    def operation_to_hash(self, operation):
        # protobuf序列化
        operation_bytes = operation.SerializeToString()

        # 计算 SHA-256 哈希值并转换为十六进制字符串
        operation_hash = hashlib.sha256(operation_bytes).hexdigest()
        return operation_hash

    # 私钥签名
    def private_sign(self, priv_key, operation_hash):
        # 将私钥从十六进制字符串转换为字节序列
        global private_key
        private_key_bytes = bytes.fromhex(priv_key)
        # 使用 Ed25519 算法从字节序列创建私钥对象
        private_key = Ed25519PrivateKey.from_private_bytes(private_key_bytes)
        # 对 operation_hash 进行 UTF-8 编码，然后使用私钥进行签名
        return private_key.sign(operation_hash.encode('utf-8'))
    
    # 通过私钥获取公钥
    def get_pubKey_from_privKey(privateKey, encryption_type):
        private_key_bytes = bytes.fromhex(privateKey)
        private_key = Ed25519PrivateKey.from_private_bytes(private_key_bytes)
        # 获取公钥
        public_key = private_key.public_key()
        # 将公钥转换为字节序列（Raw 格式）
        public_key_bytes = public_key.public_bytes(encoding=serialization.Encoding.Raw, format=serialization.PublicFormat.Raw)
        # 将公钥字节序列转换为十六进制字符串
        return binascii.hexlify(public_key_bytes).decode('utf-8')

    # 组装签名结构
    def build_signature(self, transaction, tx_hash, publicKey, signData, encryption_type):
        signature = block_pb2.TransactionSignature()
        signature.transaction.sender = transaction.sender
        signature.transaction.nonce = transaction.nonce
        signature.transaction.chain_id = transaction.chain_id
        signature.transaction.kind = transaction.kind
        signature.transaction.gas_limit = transaction.gas_limit
        signature.transaction.gas_price = transaction.gas_price
        if signature.transaction.kind == 1:
            signature.transaction.payload.transfer.to = transaction.payload.transfer.to
            signature.transaction.payload.transfer.balance = transaction.payload.transfer.balance
        elif signature.transaction.kind == 2:
            signature.transaction.payload.call.to = transaction.payload.call.to
            signature.transaction.payload.call.balance = transaction.payload.call.balance
            signature.transaction.payload.call.ctype = transaction.payload.call.ctype
            signature.transaction.payload.call.input.name = transaction.payload.call.input.name
            signature.transaction.payload.call.input.code = transaction.payload.call.input.code
            signature.transaction.payload.call.input.function = transaction.payload.call.input.function
            signature.transaction.payload.call.input.parameter = transaction.payload.call.input.parameter
        elif signature.transaction.kind == 3:
            signature.transaction.payload.sys_call.to = transaction.payload.sys_call.to
            signature.transaction.payload.sys_call.balance = transaction.payload.sys_call.balance
            signature.transaction.payload.sys_call.ctype = transaction.payload.sys_call.ctype
            signature.transaction.payload.sys_call.input.name = transaction.payload.sys_call.input.name
            signature.transaction.payload.sys_call.input.code = transaction.payload.sys_call.input.code
            signature.transaction.payload.sys_call.input.function = transaction.payload.sys_call.input.function
            signature.transaction.payload.sys_call.input.parameter = transaction.payload.sys_call.input.parameter
        elif signature.transaction.kind == 4:
            signature.transaction.payload.set_storage.key = transaction.payload.set_storage.key
            signature.transaction.payload.set_storage.value = transaction.payload.set_storage.value
            signature.transaction.payload.set_storage.delete = transaction.payload.set_storage.delete
        elif signature.transaction.kind == 5:
            signature.transaction.payload.set_archive.key = transaction.payload.set_archive.key
            signature.transaction.payload.set_archive.value = transaction.payload.set_archive.value
            signature.transaction.payload.set_archive.delete = transaction.payload.set_archive.delete
        elif signature.transaction.kind == 6:
            signature.transaction.payload.issue_asset.code = transaction.payload.issue_asset.code
            signature.transaction.payload.issue_asset.amount = transaction.payload.issue_asset.amount
            signature.transaction.payload.issue_asset.flags = transaction.payload.issue_asset.flags
            signature.transaction.payload.issue_asset.to = transaction.payload.issue_asset.to
            signature.transaction.payload.issue_asset.metadata = transaction.payload.issue_asset.metadata
        elif signature.transaction.kind == 7:
            signature.transaction.payload.pay_asset.to = transaction.payload.pay_asset.to
            signature.transaction.payload.pay_asset.code = transaction.payload.pay_asset.code
            signature.transaction.payload.pay_asset.pay_kind = transaction.payload.pay_asset.pay_kind
            signature.transaction.payload.pay_asset.amount = transaction.payload.pay_asset.amount
            signature.transaction.payload.pay_asset.authorizer = transaction.payload.pay_asset.authorizer
        signature.hash = tx_hash.encode('utf-8')
        signature.signature.public_key = bytes.fromhex(publicKey)
        signature.signature.signature_data = signData
        signature.signature.encryption_type = encryption_type
        logger.debug(
            '\nUrl：' + Config().url + '\n交易体：' + json.dumps(json.loads(json_format.MessageToJson(signature)), indent=4, ensure_ascii=False))
        return signature

    # 获取交易签名串
    def get_signature(self, sourceAddress, privateKey, nonce, type, destAddress, balance, ctype, key, value, delete, code, amount, flags, to, metadata, kind, encryption_type, name, function, parameter, authorizer, chain_id, gas_limit, gas_price):
        # 1.组装交易
        operation = self.operation_type(sourceAddress, nonce, type, destAddress, balance, ctype, key, value, delete, code, amount, flags, to, metadata, kind, name, function, parameter, authorizer, chain_id, gas_limit, gas_price)
        # 2.交易hash处理
        operation_hash = self.operation_to_hash(operation)
        # 3.私钥签名
        sign_data = self.private_sign(privateKey, operation_hash)
        # 4.组装签名结构
        publicKey = self.get_pubKey_from_privKey(privateKey, encryption_type)
        signature = self.build_signature(operation, operation_hash, publicKey, sign_data, encryption_type)
        # 5.protobuf序列化
        sign_bytes = signature.SerializeToString()
        # 6.通过 Hex 对字节码进行编码拿到 hex 字符串
        return binascii.hexlify(sign_bytes).decode()

    # 交易提交上链
    def submit_operation(self, sourceAddress, privateKey, nonce, type, destAddress=None, balance=None, ctype=None, key=None, value=None, delete=None, code=None, amount=None, flags=None, to=None, metadata=None, kind=None, encryption_type=config.encryption_type, name=None, function=None, parameter=None,  authorizer=None, chain_id=config.chain_id, gas_limit=config.gas_limit, gas_price=config.gas_price):
        sign_data = self.get_signature(sourceAddress, privateKey, nonce, type, destAddress, balance, ctype, key, value, delete, code, amount, flags, to, metadata, kind, encryption_type, name, function, parameter, authorizer, chain_id, gas_limit, gas_price)
        return self.send_raw_tx(sign_data)

```

## 配置文件内容

配置文件中主要是链相关配置及测试过程中共有的参数值：

```
url = "http://127.0.0.1:8080/v1"
chainId = "node"
encryption_type = "eddsa_ed25519" 
gas_limit = 0
gas_price = "1"
```

## 测试脚本

以下是以`transfer`交易为例的测试脚本代码:

```
# 导入BaseApi类
import BaseApi

# 赋值
genesis_address = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1"
genesis_privKey = "fc5a55e22797ed20e78b438d9e3ca873877a7b55a604dfa7531c300e743c5ef1"
destAddress = "did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e"

# 创建实例
baseApi = BaseApi()

# 获取发起者账户nonce值
nonce = baseApi.getNonce(genesis_address)

# 交易提交上链
baseApi.submit_operation(genesis_address, genesis_privKey, nonce + 1, 1, destAddress=destAddress, balance=10)
```

## 运行说明

1. 确保已按照安装指南安装了所有的Python库。

2. 将源码中Protobuf原始文件编译为Python对象，生成`block_pb2.py`和`common_pb2.py`文件

3. 创建一个名为`baseApi.py`的文件，并将核心代码粘贴进去。

4. 创建一个名为`config.py`的文件，并将配置文件内容粘贴进去。

5. 创建一个名为`test_transfer.py`的文件，并将测试脚本粘贴进去。

6. 在终端中运行测试脚本：

   ```
   python test_transfer.py
   ```

7. 您将看到交易信息及上链结果。

