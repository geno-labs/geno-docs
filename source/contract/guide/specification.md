# 合约语言规范与编程风格

## 合约结构
一个文件就是一个合约，你可以引用`rust`标准库，帮助你写合约。
Geno通过使用宏，标注合约内部的数据以及方法，明确其作用和功能；例如初始化函数，调用函数，函数参数，事件等；都是通过`rust`宏定义的。

定义`Storage`合约的初始化方法；
```rust
#[init(contract="Storage")]
fn contract_init(ctx: &impl HasInitContext) -> InitResult<State> {
    ... ...
}
```

定义`Storage`合约的调用方法；
```rust
#[receive(contract="Storage",name="set")]
fn contract_set<A: HasActions>(
    ctx: &impl HasReceiveContext<()>,
    logger: &mut impl HasLogger,
    state: &mut State,
) -> Result<A, ReceiveError> {
    ... ...
}
```

定义`Storage`合约的状态；
```rust
#[contract_state(contract = "Storage")]
#[derive(Serialize, SchemaType)]
pub struct State {
    height: u64,
    time: Timestamp,
    owner: AccountAddress,
}
```

定义`Storage`合约方法用到的参数；
```rust
#[derive(Serialize, SchemaType)]
struct Key {
    address : AccountAddress,
}
```

定义`Storage`合约的事件；
```rust
#[contract_event(contract = "Storage")]
#[derive(Serialize, SchemaType)]
enum Event {
    SET(AccountAddress, String),
    GET(AccountAddress, AccountAddress),
}
```

## 合约状态



## 合约方法



## 合约事件



## 合约交互



