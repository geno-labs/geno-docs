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
合约状态表示智能合约实例之后，与合约实例关联的数据；这个状态是用户在编写智能合约的时候定义的；用户可以通过交易和智能合约进行交互，修改与之关联的状态。我们将合约状态存储到链上，作为持久化存储；每次需要更新合约状态时，需要先从链上取出合约状态，状态更新完后，将新的状态存储到链上；  
 
使用`#[contract_state]`宏，定义合约状态。合约的状态是一个struct类型。用户可以根据需求自行定义内容。  
```rust
#[contract_state(contract = "Storage")]
#[derive(Serialize, SchemaType)]
pub struct State {
    height: u64,
    time: Timestamp,
    owner: AccountAddress,
}
```

## 合约方法

我们把智能合约内的方法分为两类，一类叫初始化函数，一类叫调用函数。

### 初始化函数 
初始化函数方法，主要作用是初始化智能合约的状态。这类方法每个智能合约只有一个，且只在合约创建的时候执行一次，之后都不会在调用；这类方法，不需要用户调用，是自动执行的；

我们可以通过使用`#[init]`宏去标记一个函数，将其定义为初始化函数，例如：
```rust
#[init(contract="erc20")]
fn contract_init<I:HasInitContext<()>, L:HasLogger>(ctx:&I,logger:&mut L)->InitResult<State>{
    ... ...
}
```

### 调用函数
调用方法，这类方法主要是给用户使用的，用户可以通过这类方法与智能合约交互，进行更新、查询等操作。这类方法是由用户使用交易进行触发的，没有次数的限制。
 
使用`#[receive(…)]`宏，标记一个调用方法，同时还需要使用`name`去指定这个调用方法的名字；
```rust
#[receive(contract="erc20",name="receive")]
fn contract_receive<A:HasActions>(
    ctx:&impl HasReceiveContext<()>,
    state:&mut State,
)->Result<A,ReceiveError>{
    ... ...
}
``` 

#### 参数
用户可以通过`parameter`属性去指定方法的参数类型。
```Rust
#[receive(contract="erc20",name="receive", parameter="Request")]
fn contract_receive<A:HasActions>(
    ctx:&impl HasReceiveContext<()>,
    state:&mut State,
)->Result<A,ReceiveError>{
    let req: Request = ctx.parameter_cursor().get()?;
    ... ...
}
``` 

### 返回值
用户可以通过`result`属性去指定方法的返回值类型。
```rust
#[receive(contract="erc20",name="receive", parameter="Request", result = "Information")]
fn contract_receive<A:HasActions>(
    ctx:&impl HasReceiveContext<()>,
    state:&mut State,
)->Result<A,ReceiveError>{
    ... ...
    ctx.result(Information {
            name: tokeninfo.name,
            total_supply: tokeninfo.total_supply,
            owner: ctx.owner(),
        });
}
``` 

### 收发代币
表示该方法是否可以接受代币。使用`payable`属性标识。

```rust
#[receive(contract="erc20",name="receive", parameter="Request", result = "Information"，payable)]
fn contract_receive<A:HasActions>(
    ctx:&impl HasReceiveContext<()>,
    state:&mut State,
    amount: Amount,
)->Result<A,ReceiveError>{
    let num = amout;
    ... ...
}
``` 


## 合约事件

当智能合约在执行的过程中，可能会发生一些比较重要的事件，需要记录或者通知用户；
我们在写合约的时候，使用合约事件标记发生事件的相关信息；当用户调用合约，并触发事件之后，事件中的参数存储到交易收据的日志字段中，日志是一种特殊的数据结构，这些日志与合约地址相关联，并随交易收据记录到区块链中。上层应用，如果监听了某个事件，则当该事件发生时，便会触发应用相应操作。

### 创建事件 

在Geno中，用户可使用`enum`自行定义事件,每个成员都是一种事件。定义完事件之后，需要使用`#[contract_event]`宏，标记该合约事件。例如：
```Rust
#[contract_event(contract="erc20")]
enum Event{
    Transfer(AccountAddress,AccountAddress,u64),
    Approval(AccountAddress,AccountAddress,u64),
}
```
上述代码中，我们定义了两个事件`Transfer(AccountAddress,AccountAddress,u64)`,`Approval(AccountAddress,AccountAddress,u64)`。每个事件都有三个参数，两个地址以及一个整型变量。

### 触发事件

在方法中，需要添加 `enable_logger`表示使能合约事件，然后在参数中添加`logger:&mut impl HasLogger`。
在方法执行的过程中，需要触发事件的时候，我们就可以使用`logger.log()`方法，来触发事件。
```rust
#[receive(contract="erc20",name="receive", parameter="Request", enable_logger)]
fn contract_receive<A:HasActions>(
    ctx:&impl HasReceiveContext<()>,
    logger:&mut impl HasLogger,
    state:&mut State,
) -> Result<A,ReceiveError> {
    ... ...
    logger.log(&Event::Transfer(sender_address,receiver_address,amount))?;
    ... ...
}
```
等到方法执行完成后，应用就可以查询到该事件通知；


