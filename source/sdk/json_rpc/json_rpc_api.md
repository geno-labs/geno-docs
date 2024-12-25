# send_raw_tx 

提交签名交易

## Parameters

+ `signed_tx_hex`: `string` 类型， 交易签名后的 hex 数据

## Returns

+ `hash`: `string` 类型，交易哈希

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8080/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"send_raw_tx",
    "params":[
        "0aad010a336469643a67656e6f3a30786636623032613264343762383465383435623765333632333335356630343162636233366461663110011a336469643a67656e6f3a30786636623032613264343762383465383435623765333632333335356630343162636233366461663120012a3b0a390a336469643a67656e6f3a30783161376232373135396135396564323830323935656664333231613835383662343636633539313810a09c01420012730a2002cf651570988a1c474384c2257aaa0cb07417ae5cd0b25a638abad39c4061bf1240170e6c2cf2e82b2fa32432b637492f9487b62a34c17df0000124b1b91741b7c0cec6e1a0d06686fc5fd3c83e659c5f5ac29f0a3beb219c765f122a6f0c200e0d1a0d65646473615f656432353531391a4061613961613962373162623165323832636639356662333661336634666430323034353666343638336661646430626165333134656234663539653031393433"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "self_chain_id",
    "jsonrpc": "2.0",
    "id": 1,
    "result": "aa9aa9b71bb1e282cf95fb36a3f4fd020456f4683fadd0bae314eb4f59e01943",
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# call

查询合约

## Parameters

+ `address`: `string` 类型，合约地址

+ `function`: `string` 类型，合约函数名

+ `parameter`: `string` 类型，函数参数

+ `ctype`: `uint` 类型，虚拟机类型：0 - wasm

## Returns

+ `code`: `i32` 类型，状态码
+ `result`: `string` 类型，执行结果
+ `message`: `string` 类型，错误提示信息
+ `gas_used`: `u64` 类型，使用的 gas 数量
+ `events`: `[]` 结构体数组类型，交易包含的合约事件，结构体如下：
  + `topic`: `u64` 类型，topic
  + `tx_id`: `u64` 类型，id
  + `contract_name`: `u64` 类型，合约名称
  + `contract_version`: `u64` 类型，合约版本
  + `event_data`: `string[]` 类型，事件列表

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"call",
    "params":[
        "did:geno:0xc8ebf548efe3eb801e52014abadd25213918417b",
        "get",
        "{\"key\":\"test-call\"}",
        0
    ],
    "id":1
}'
```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "code": 0,
        "events": [
            {
                "contract_address": "did:geno:0xc8ebf548efe3eb801e52014abadd25213918417b",
                "contract_name": "contractDefault-stm",
                "event_data": [],
                "topic": [],
                "tx_id": ""
            }
        ],
        "gas_used": 0,
        "message": "{\n  \"value\": \"call-contract\"\n}",
        "result": ""
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_txs

获取区块高度查询区块内的交易

## Parameters

+ `height`: `uint256` 类型 区块高度


## Returns

+ `txs`: `[]` 结构体数组类型，交易列表。结构体如下：

  + `height`: `u64` 类型，区块高度

  + `hash`: `string` 类型，交易哈希

  + `from`: `int32` 类型，交易发送方地址

  + `nonce`: `u64` 类型，Nonce

  + `chain_id`: `string` 类型，链 Id

  + `gas_limit`: `u64` 类型，错误码

  + `gas_price`: `string` 类型，错误提示信息

  + `kind`: `i32` 区块高度

  + `payload`: `Enum` 结构体枚举类型，Payload , 结构体如下：
    | Key          | `tx_type` | 字段                                                                                                            | 说明        | 示例                                                                                                                                                               |
    | ------------ | --------- | --------------------------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
    | `Transfer`   | `1`       | `to`: `string`类型，转账接收方地址 <br> `balance`: `number` 类型，转账数量                                      | 转账说明xxx | <pre lang="json"> { <br>   "Transfer": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |
    | `Call`       | `2`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "Call": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>       |
    | `SysCall`    | `3`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "SysCall": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>    |
    | `SetStorage` | `4`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetStorage": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
    | `SetArchive` | `5`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetArchive": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
    | `IssueAsset` | `6`       | `code`: `string` 类型，<br> `amount`: `number` 类型 <br> `kind`: `number` 类型                                  | 转账说明xxx | <pre lang="json"> { <br>   "IssueAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
    | `PayAsset`   | `7`       | `key`: `string`类型，<br> `value`: `string` 类型，<br> `value_type`: `string` 类型，<br> `encoded`: `bool` 类型 | 转账说明xxx | <pre lang="json"> { <br>   "PayAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |

  + `err_code`: `i32` 类型，状态码

  + `err_msg`: `string` 类型，错误提示信息
  
  + `call_data`: `` 结构体类型，交易执行结果
    
    + `code`: `i32` 类型，状态码
  
    + `result`: `string` 类型，执行结果
    
    + `message`: `string` 类型，错误提示信息
    
    + `gas_used`: `u64` 类型，使用的 gas 数量
    
    + `events`: `[]` 结构体数组类型，交易包含的合约事件，结构体如下：
      
      + `topic`: `u64` 类型，topic
      
      + `tx_id`: `u64` 类型，时间
      
      + `contract_name`: `u64` 类型，合约名称
      
      + `contract_version`: `u64` 类型，合约版本
      
      + `event_data`: `string[]` 类型，事件列表

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_txs",
    "params":[
        3525
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e",
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "chain_id": "2024",
            "from": "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
            "gas_limit": 0,
            "gas_price": "0",
            "hash": "d4c1af0694bb07d0319443d263f91833b092946f539447a787fcb010abba35a0",
            "height": 3525,
            "kind": 1,
            "message": "",
            "nonce": 1,
            "payload": {
                "Transfer": {
                    "balance": 20000,
                    "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918"
                }
            },
            "result": {
                "code": 0,
                "events": [],
                "gas_used": 0,
                "message": "",
                "result": ""
            },
            "status_code": 0
        }
    ],
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_tx

## Parameters

+ `hash`: `string` 类型，交易哈希

## Returns

+ `height`: `u64` 类型，区块高度

+ `hash`: `string` 类型，交易哈希

+ `from`: `int32` 类型，交易发送方地址

+ `nonce`: `u64` 类型，Nonce

+ `chain_id`: `string` 类型，链 Id

+ `gas_limit`: `u64` 类型，错误码

+ `gas_price`: `string` 类型，错误提示信息

+ `kind`: `i32` 区块高度

+ `payload`: `Enum` 结构体枚举类型，Payload , 结构体如下：
  | Key          | `tx_type` | 字段                                                                                                            | 说明        | 示例                                                                                                                                                               |
  | ------------ | --------- | --------------------------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
  | `Transfer`   | `1`       | `to`: `string`类型，转账接收方地址 <br> `balance`: `number` 类型，转账数量                                      | 转账说明xxx | <pre lang="json"> { <br>   "Transfer": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |
  | `Call`       | `2`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "Call": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>       |
  | `SysCall`    | `3`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "SysCall": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>    |
  | `SetStorage` | `4`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetStorage": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `SetArchive` | `5`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetArchive": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `IssueAsset` | `6`       | `code`: `string` 类型，<br> `amount`: `number` 类型 <br> `kind`: `number` 类型                                  | 转账说明xxx | <pre lang="json"> { <br>   "IssueAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `PayAsset`   | `7`       | `key`: `string`类型，<br> `value`: `string` 类型，<br> `value_type`: `string` 类型，<br> `encoded`: `bool` 类型 | 转账说明xxx | <pre lang="json"> { <br>   "PayAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |

+ `err_code`: `i32` 类型，状态码

+ `err_msg`: `string` 类型，错误提示信息

+ `call_data`: `` 结构体类型，交易执行结果
  
  + `code`: `i32` 类型，状态码

  + `result`: `string` 类型，执行结果
  
  + `message`: `string` 类型，错误提示信息
  
  + `gas_used`: `u64` 类型，使用的 gas 数量
  
  + `events`: `[]` 结构体数组类型，交易包含的合约事件，结构体如下：
    
    + `topic`: `u64` 类型，topic
    
    + `tx_id`: `u64` 类型，时间
    
    + `contract_name`: `u64` 类型，合约名称
    
    + `contract_version`: `u64` 类型，合约版本
    
    + `event_data`: `string[]` 类型，事件列表

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_tx",
    "params":[
        "ecff08229a8ff43df8e79489272f85b39278d4ef46f10ef4ce57719090f8389a"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "chain_id": "2024",
        "from": "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
        "gas_limit": 0,
        "gas_price": "0",
        "hash": "ecff08229a8ff43df8e79489272f85b39278d4ef46f10ef4ce57719090f8389a",
        "height": 4509,
        "kind": 1,
        "message": "",
        "nonce": 2,
        "payload": {
            "Transfer": {
                "balance": 20000,
                "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918"
            }
        },
        "result": {
            "code": 0,
            "events": [],
            "gas_used": 0,
            "message": "",
            "result": ""
        },
        "status_code": 0
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_pending_tx

获取 `pending` 状态即交易池中的交易

## Parameters

+ `hash`: `string` 类型，交易哈希

## Returns

+ `hash`: `string` 类型，交易哈希

+ `from`: `int32` 类型，交易发送方地址

+ `nonce`: `u64` 类型，Nonce

+ `chain_id`: `string` 类型，链 Id

+ `gas_limit`: `u64` 类型，错误码

+ `gas_price`: `string` 类型，错误提示信息

+ `kind`: `i32` 区块高度

+ `payload`: `Enum` 结构体枚举类型，Payload , 结构体如下：
  | Key          | `tx_type` | 字段                                                                                                            | 说明        | 示例                                                                                                                                                               |
  | ------------ | --------- | --------------------------------------------------------------------------------------------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
  | `Transfer`   | `1`       | `to`: `string`类型，转账接收方地址 <br> `balance`: `number` 类型，转账数量                                      | 转账说明xxx | <pre lang="json"> { <br>   "Transfer": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |
  | `Call`       | `2`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "Call": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>       |
  | `SysCall`    | `3`       | `to`: `string`类型，调用的合约地址<br> `balance`: `number` 类型 <br> `input`: `Call` 类型                       | 转账说明xxx | <pre lang="json"> { <br>   "SysCall": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>    |
  | `SetStorage` | `4`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetStorage": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `SetArchive` | `5`       | `key`: `string` 类型，<br> `value`: `string` 类型 <br> `delete`: `bool` 类型                                    | 转账说明xxx | <pre lang="json"> { <br>   "SetArchive": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `IssueAsset` | `6`       | `code`: `string` 类型，<br> `amount`: `number` 类型 <br> `kind`: `number` 类型                                  | 转账说明xxx | <pre lang="json"> { <br>   "IssueAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre> |
  | `PayAsset`   | `7`       | `key`: `string`类型，<br> `value`: `string` 类型，<br> `value_type`: `string` 类型，<br> `encoded`: `bool` 类型 | 转账说明xxx | <pre lang="json"> { <br>   "PayAsset": { <br>     "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918", <br>     "balance": 20000 <br>    } <br> } </pre>   |


## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_pending_tx",
    "params":[
        "1acfe96f2610eec6ff7cfa12a08a53c050dd560b5b9157939725826d3b78e790"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "chain_id": "2024",
        "from": "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
        "gas_limit": 0,
        "gas_price": "0",
        "hash": "1acfe96f2610eec6ff7cfa12a08a53c050dd560b5b9157939725826d3b78e790",
        "kind": 1,
        "nonce": 3,
        "payload": {
            "Transfer": {
                "balance": 20000,
                "to": "did:geno:0x1a7b27159a59ed280295efd321a8586b466c5918"
            }
        }
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_account

获取账户信息

## Parameters

+ `addr`: `string` 类型，账户地址

## Returns

+ `address`: `string` 类型，钱包地址

+ `nonce`: `uint256` 类型，nonce

+ `balance`: `uint256` 类型，账户余额

+ `account_type`: `bool` 类型，是否为合约账户：`true` - 是, `false` - 否 

+ `contract`: 结构体类型，合约账户信息
  
  + `name`: `String` 类型，合约名称
  
  + `creator`: `String` 类型，合约创建者

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8080/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_account",
    "params":[
        "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "account_type": false,
        "address": "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
        "balance": 49920000,
        "contract": {
            "creator": "",
            "name": ""
        },
        "nonce": 4
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_nonce

获取账户 Nonce 值

## Parameters

+ `addr`: `string` 类型，账户地址

## Returns

+ `nonce`: `uint256` 类型，Nonce

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8080/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_nonce",
    "params":[
        "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": 1,
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_block

获取最新或历史区块信息

## Parameters

+ `height`: `uint256` 类型，`0` - 代表最新区块

## Returns

+ `height`: `uint256` 类型，区块高度

+ `timestamp`: `uint256` 类型，区块时间戳

+ `hash`: `string` 类型，区块 Hash

+ `parent_hash`: `string` 类型，上一个区块 Hash

+ `state_hash`: `string` 类型，区块状态 Hash

+ `proposal_hash`: `string` 类型，提案 Hash

+ `version`: `uint256` 类型，Version

+ `tx_count`: `uint256` 类型，区块交易总数

+ `total_tx_count`: `uint256` 类型，历史交易总数

+ `chain_id`: `string` 类型，链 Id

+ `proposer`: `string` 类型，区块提案者地址
  
## Example

### Request

```shell

# get lastest block
curl --location 'http://127.0.0.1:8080/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_block",
    "params":[
        0
    ],
    "id":1
}'

or

# get block by height

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_block",
    "params":[
        2
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "self_chain_id",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "chain_id": "2024",
        "hash": "b5e19a0218b4b642c679a9cf50a907fa8f4c247f895e9254d8353c6cc31a6e8e",
        "height": 3074,
        "parent_hash": "f47bc2a4d07d155c6007943fb4f10878b6c97837a08087b6edc2f31c3266187c",
        "proposal_hash": "c6c7e8ffb33b0615926a331921c9565603fd737cee59103560891621f8f4904c",
        "proposer": "did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e",
        "state_hash": "4adae174a458ce99c13c70216c99047a54c9b46ee5920ba092f58d7dc07e1de4",
        "timestamp": 1732005972499,
        "total_tx_count": 0,
        "tx_count": 0,
        "version": 1000
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_block_height

获取最新区块高度

## Parameters

+ 无

## Returns

+ `height`: `uint256` 类型，最新区块高度
  
## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_block_height",
    "params":[
        
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": 4562,
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_storage

获取账户 storage 数据

## Parameters

+ `address`: `string` 类型，账户地址

+ `key`: `string` 类型，Key

## Returns

+ `key`: `string` 类型，Key

+ `value`: `string` 类型，Value

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_storage",
    "params":[
        "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
        "storage-test"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "key": "storage-test",
        "value": "VkdWemRGTmxkRk4wYjNKaFoyVT0="
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_archive

获取账户 archive 数据

## Parameters

+ `address`: `string` 类型，账户地址

+ `key`: `string` 类型，Key

## Returns

+ `key`: `string` 类型，Key

+ `value`: `string` 类型，Value
  
## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_archive",
    "params":[
        "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1",
        "archive-test"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "key": "archive-test",
        "value": "VkdWemRGTmxkRUZ5WTJocGRtVT0="
    },
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_centralized_assets

获取通用资产列表

## Parameters

+ 无

## Returns

+ `assets`: `[]` 结构体数组类型，资产列表，结构体如下：

  + `infos`: `[]` 结构体数组类型，发行方地址，结构体如下：
    
    + `issuer`: `string` 类型，资产发行人
    
    + `code`: `string` 类型，资产编号 
    
    + `total`: `u64` 类型，发行数量
    
    + `metadata`: `string` 类型，扩展数据字段

  + `issuers`: `string[]` 字符串数组类型，资产发行人列表
    
    

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_centralized_assets",
    "params":[
      
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "infos": [
                {
                    "code": "USDT",
                    "issuer": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
                    "metadata": "",
                    "total": 4000000000
                },
                {
                    "code": "BTC",
                    "issuer": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
                    "metadata": "",
                    "total": 5000000000
                },
                {
                    "code": "ETH",
                    "issuer": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
                    "metadata": "",
                    "total": 2400000000
                },
                {
                    "code": "XRP",
                    "issuer": "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99",
                    "metadata": "",
                    "total": 2600000000
                }
            ],
            "issuers": [
                "did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99"
            ]
        }
    ],
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_assets

获取账户资产列表

## Parameters

+ `addr`: `string` 类型，账户地址

## Returns

+ `assets`: `[]` 结构体数组类型，资产列表，结构体如下：

  + `issuer`: `string` 类型，发行人地址

  + `code`: `string` 类型，资产编号

  + `flags`: `u64` 类型，资产状态：0 - 正常，1 - 冻结，2 - 解冻

  + `authorize_amounts`: `[]` 结构体数组类型，资产授权列表，结构体如下：
    
    + `amount`: `u64` 类型，授权金额
    
    + `authorizer`: `string` 类型，授权地址

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_assets",
    "params":[
      "did:geno:0x610903ba89b62cc25e6b81608b13cc4004cdb1d7"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "amount": 1000,
            "authorize_amounts": [
                {
                    "amount": 5000,
                    "authorizer": "did:geno:0xa6ebd2a4441fa624dee2356a8ad89ba01e0ed289"
                }
            ],
            "code": "USDT",
            "flags": 0
        },
        {
            "amount": 5000000000,
            "authorize_amounts": [],
            "code": "BTC",
            "flags": 0
        }
    ],
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# get_validators

获取区块验证者

## Parameters

+ `height`: `uint256` 类型 区块高度

## Returns

+ `address`: `string` 类型，验证者地址

+ `pledge`: `uint256` 类型，抵押金数量


## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"get_validators",
    "params":[
        13453
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        {
            "address": "did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e",
            "pledge": 0
        }
    ],
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# issue_vc

发布证书

## Parameters

+ `subject_id`: `string` 类型，

+ `private_key`: `string` 类型，

+ `object`: `string` 类型，

## Returns

+ `result`: `string` 类型，

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data ' {
    "jsonrpc":"2.0",
    "method":"issue_vc",
    "params":[
        "did:geno:0x6d99abe0ee7a7a5cf137ce729ad4545fa3c5dfad",
		"210837a9245a0ca603ca5e068bc243bf3f383b62d80b7b912c688bc53106e779",
		"{\"cdi\":\"did:gdt:0xa2a9b6e2fd7f94cebf7b37e0156edcc3bad2347d\"}"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": "{\"@context\":[\"https://www.w3.org/2018/credentials/v1\"],\"types\":\"VerifiableCredential\",\"credentialSubject\":{\"id\":\"did:geno:0x6d99abe0ee7a7a5cf137ce729ad4545fa3c5dfad\",\"properties\":{\"cdi\":\"did:gdt:0xa2a9b6e2fd7f94cebf7b37e0156edcc3bad2347d\"}},\"issuer\":\"did:geno:0x6d99abe0ee7a7a5cf137ce729ad4545fa3c5dfad\",\"issuanceDate\":\"2099-12-31T00:00:00+08:00\",\"proof\":{\"type\":\"JcsEd25519Signature2020\",\"signatureValue\":\"e39cefb10dd004ad6ba72511f767570f2d5479638abedd352ad0bea335e9853ec4fc85cc9d935afb4cac32848fcc42b0f93130022b862bdc609107e2e1564e0b\",\"verificationMethod\":\"ee33cbe7dc34dc19ad86ec205b001932150d3dcd0830aa378c815cbaf64ab5f4\"},\"id\":\"https://genodigitaltechnology.com/credentials/2996958387\"}",
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

```

# verify_vc

验证证书

## Parameters

+ `vc`: `string` 类型，

## Returns

+ `result`: `bool` 类型，

## Example

### Request

```shell

curl --location 'http://127.0.0.1:8081/v1' \
--header 'Content-Type: application/json' \
--data-raw ' {
    "jsonrpc":"2.0",
    "method":"verify_vc",
    "params":[
        "{\"@context\":[\"https://www.w3.org/2018/credentials/v1\"],\"types\":\"VerifiableCredential\",\"credentialSubject\":{\"id\":\"did:geno:0x6d99abe0ee7a7a5cf137ce729ad4545fa3c5dfad\",\"properties\":{\"cdi\":\"did:gdt:0xa2a9b6e2fd7f94cebf7b37e0156edcc3bad2347d\"}},\"issuer\":\"did:geno:0x6d99abe0ee7a7a5cf137ce729ad4545fa3c5dfad\",\"issuanceDate\":\"2099-12-31T00:00:00+08:00\",\"proof\":{\"type\":\"JcsEd25519Signature2020\",\"signatureValue\":\"e39cefb10dd004ad6ba72511f767570f2d5479638abedd352ad0bea335e9853ec4fc85cc9d935afb4cac32848fcc42b0f93130022b862bdc609107e2e1564e0b\",\"verificationMethod\":\"ee33cbe7dc34dc19ad86ec205b001932150d3dcd0830aa378c815cbaf64ab5f4\"},\"id\":\"https://genodigitaltechnology.com/credentials/2996958387\"}"
    ],
    "id":1
}'

```
### Response

```json

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": true,
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}

or

{
    "chain_id": "2024",
    "jsonrpc": "2.0",
    "id": 1,
    "result": false,
    "error": {
        "code": 0,
        "message": "Success",
        "data": null
    }
}
```










