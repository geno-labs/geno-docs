# 隐私计算

## get_txs

获取区块高度查询区块内的交易

### Parameters

+ `height`: `uint256` 类型 区块高度


### Returns

+ `hash`: `string` 类型，交易哈希

+ `tx_type`: `int32` 类型，交易类型

+ `transaction`: `` 类型，交易信息

+ `signatures`: `` 类型，签名信息

+ `error_code`: `string` 类型，错误码

+ `error_msg`: `string` 类型，错误提示信息

+ `ledger_seq`: `string` 区块高度

+ `close_time`: `` 类型，时间

+ `actual_fee`: `string` 类型，手续费

+ `events`: `string` 类型，事件


## get_tx

### Parameters

+ `hash`: `string` 类型，交易哈希


### Returns

+ `hash`: `string` 类型，交易哈希

+ `tx_type`: `int32` 类型，交易类型

+ `transaction`: `` 类型，交易信息

+ `signatures`: `` 类型，签名信息

+ `error_code`: `string` 类型，错误码

+ `error_msg`: `string` 类型，错误提示信息

+ `ledger_seq`: `string` 区块高度

+ `close_time`: `` 类型，时间

+ `actual_fee`: `string` 类型，手续费

+ `events`: `string` 类型，事件

## get_pending_txs

获取 `pending` 状态即交易池中的交易

### Parameters

+ null

### Returns

+ `hash`: `string` 类型，交易哈希

+ `tx_type`: `int32` 类型，交易类型

+ `transaction`: `` 类型，交易信息

+ `signatures`: `` 类型，签名信息

+ `error_code`: `string` 类型，错误码

+ `error_msg`: `string` 类型，错误提示信息

+ `ledger_seq`: `string` 区块高度

+ `close_time`: `` 类型，时间

+ `actual_fee`: `string` 类型，手续费

+ `events`: `string` 类型，事件
