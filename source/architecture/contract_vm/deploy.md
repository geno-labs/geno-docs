# 部署、升级、治理流程

合约的相关操作可以参看`Json Rpc Api`.未来方便观察交易结构，这里我们使用测试接口`test_sign_tx`，由链上帮助我们进行签名操作。

+ `from`: `string` 类型，发送方地址
+ `nonce`: `number` 类型，发送方账户nonce
+ `chain_id`: `string` 类型，链Id, 默认值 `2024`
+ `tx_type`: `number` 类型，交易类型
+ `payload`: `Call` 类型，合约交易数据。
    - `to`: `string`类型，调用的合约地址<br> 
    - `balance`: `number` 类型 <br> 
    - `ctype`:`number` 类型, vm类型
    - `input`: 
        + `name`: `string` 类型，合约名称
        + `code`: `string` 类型，合约字节码
        + `function`: `string` 类型，调用函数
        + `parameter`: `string` 类型，函数的参数
+ `fee_limit`: `number` 类型，gas limit
+ `gas_price`: `string` 类型，gas price
+ `secret`: 私钥
+ `type`: 签名算法类型

## 合约部署

部署合约的时候，`to`地址为空，`code`必须不为空，`name`也不能为空，其他参数可以随意；

```json
    "Call": {
        "to": "",
        "balance": 0,
        "ctype": 0,
        "input":{
            "name":"Storage",
            "code":"0aad01...0a3364",
            "function":"",
            "parameter":""
        }
    }
```

## 合约部署

调用合约的时候，`to`地址不能为空，`function`也不能为空，其他参数可以随意；

```json
    "Call": {
        "to": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
        "balance": 0,
        "ctype": 0,
        "input":{
            "name":"",
            "code":"",
            "function":"get",
            "parameter":""
        }
    }
```

## 合约升级

升级合约的时候，`to`地址不能为空，`code`必须不为空，其他参数可以随意；

```json
    "Call": {
        "to": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
        "balance": 0,
        "ctype": 0,
        "input":{
            "name":"",
            "code":"0aad01...0a3364",
            "function":"",
            "parameter":""
        }
    }
```
