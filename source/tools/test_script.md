# Python 测试脚本组件

区块链技术依赖于其健壮性和安全性，因此测试在开发过程中至关重要。Python 因其强大的生态系统和易用性，成为区块链测试脚本编写的热门选择。本文档旨在介绍如何使用 Python 来编写和使用区块链的测试脚本。

## 环境要求

- Python：Python 3.9 +。
- Protobuf：3.12.4

## 脚本说明

1. 将源码中Protobuf原始文件编译为Python对象，生成`block_pb2.py`和`common_pb2.py`

2. 导入必要的库

   这些库分别用于数据转换、哈希计算、签名操作及Protobuf数据格式化。

   ```
   import binascii
   import hashlib
   import json
   
   from cryptography.hazmat.primitives import serialization
   from cryptography.hazmat.primitives.asymmetric.ed25519 import Ed25519PrivateKey
   from google.protobuf import json_format
   ```

3. 组装交易

   创建一个 `Transaction` 对象并设置各交易参数，以`transfer`交易为例：

   ```
   def build_transaction():
       tx = block_pb2.Transaction()
       tx.sender = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1"
       tx.nonce = "1"
       tx.chain_id = "node"
       tx.kind = 1
       tx.gas_limit = 0
       tx.gas_price = "1"
       tx.payload.transfer.to = "did:geno:0xc5928f98f6d905cb96cc407f22d4b3f7904bf803"
       tx.payload.transfer.balance = "100"
       return tx
   ```

4. 交易哈希处理

   使用`Protobuf`序列化交易对象，然后计算其`SHA-256`哈希值并转换为十六进制字符串。

   ```
   def transaction_to_hash(tx):
       operation_bytes = tx.SerializeToString()
       transaction_hash = hashlib.sha256(operation_bytes).hexdigest()
       return transaction_hash
   ```

5. 私钥签名

   将私钥从十六进制字符串转换为字节序列，然后使用`Ed25519`算法对哈希值进行签名。

   ```
   def privateKey_sign():
       private_key_bytes = bytes.fromhex("fc5a55e22797ed20e78b438d9e3ca873877a7b55a604dfa7531c300e743c5ef1")
       private_key = Ed25519PrivateKey.from_private_bytes(private_key_bytes)
       sign_data = private_key.sign(operation_hash.encode('utf-8'))
       return sign_data
   ```

6. 获取公钥

   通过私钥生成公钥，并将公钥转换为十六进制字符串。

   ```
   def get_pubkey_from_privkey(private_key):
       private_key_bytes = bytes.fromhex(private_key)
       private_key = Ed25519PrivateKey.from_private_bytes(private_key_bytes)
       public_key = private_key.public_key()
       public_key_bytes = public_key.public_bytes(encoding=serialization.Encoding.Raw, format=serialization.PublicFormat.Raw)
       publicKey = binascii.hexlify(public_key_bytes).decode('utf-8')
       return publicKey
   ```

7. 组装签名结构

   创建 `TransactionSignature` 对象并设置交易详细信息、哈希值、签名数据和加密类型。

   ```
   def build_signature(tx, publicKey, sign_data):
       signature = block_pb2.TransactionSignature()
       signature.transaction.sender = tx.sender
       signature.transaction.nonce = tx.nonce
       signature.transaction.chain_id = tx.chain_id
       signature.transaction.kind = tx.kind
       signature.transaction.gas_limit = tx.gas_limit
       signature.transaction.gas_price = tx.gas_price
       signature.transaction.payload.transfer.to = tx.payload.transfer.to
       signature.transaction.payload.transfer.balance = tx.payload.transfer.balance
       signature.hash = operation_hash.encode('utf-8')
       signature.signature.public_key = bytes.fromhex(publicKey)
       signature.signature.signature_data = sign_data
       signature.signature.encryption_type = "eddsa_ed25519"
       return signature
   ```

8. Protobuf序列化并编码

   使用`Protobuf`序列化签名结构，将序列化后的字节数据转换为十六进制字符串。

   ```
   def get_transaction_sign(signature):
       sign_bytes = signature.SerializeToString()
       transactino_sign = binascii.hexlify(signature).decode()
       return transactino_sign
   ```

9. 提交交易

   将交易签名后hex数据提交上链。

   ```
   def send_raw_tx(transactino_sign):
   	headers = {
               "Content-Type": "application/json"
       }
       
   	data = {
               "method": "send_raw_tx",
               "params": transactino_sign,
               "jsonrpc": "2.0",
               "id": 1
       }
       requests.post(url, headers=headers, data=json.dumps(data))
   ```

   