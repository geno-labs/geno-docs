# 智能合约编写介绍与示例

## 智能合约实例

我们编写一个简单智能合约————的存储与读取数据————作为示例；在这个实例中，用户使用自己的账户地址作为key值，`Info`作为value。用户可以通过调用`set`函数将准备好的`Info`存到链上；然后通过`get`函数，以及准备好的`Key`参数，读取到存储的数据。


```
    #![cfg_attr(not(feature = "std"), no_std)]
    use concordium_std::{collections::*, *};


    #[derive(Serialize, SchemaType)]
    struct Info {
        id:         u64,
        name:       String,
        storage:    String,
    }

    #[derive(Serialize, SchemaType)]
    struct Key {
        address : AccountAddress,
    }

    #[contract_state(contract = "Storage")]
    #[derive(Serialize, SchemaType)]
    pub struct State {
        height: u64,
        time: Timestamp,
        owner: AccountAddress,
    }

    #[contract_event(contract = "Storage")]
    #[derive(Serialize, SchemaType)]
    enum Event {
        SET(AccountAddress, String),
        GET(AccountAddress, AccountAddress),
    }

    #[derive(Debug, PartialEq, Eq, Reject)]
    enum ReceiveError {
        ParseParams,
        OnlyAccounts,
        MoreThanAllowed,
        LogFull,
        InsufficientFunds,
        LogMalformed,
        InUsed,
        NotFound,
    }
    impl From<LogError> for ReceiveError {
        fn from(le: LogError) -> Self {
            match le {
                LogError::Full => Self::LogFull,
                LogError::Malformed => Self::LogMalformed,
            }
        }
    }

    impl From<ParseError> for ReceiveError {
        fn from(_: ParseError) -> Self { ReceiveError::ParseParams }
    }

    #[init(contract = "Storage", payable)]
    #[inline(always)]
    fn contract_init<I: HasInitContext<()>>(
        ctx: &I,
        amount: Amount,
    ) -> InitResult<State> {
        let state = State {
            height:ctx.metadata().height(),
            time:ctx.metadata().slot_time(),
            owner: ctx.init_origin(),
        };
        Ok(state)
    }

    #[receive(contract = "Storage", name = "get", parameter = "Key", result="Info", enable_logger)]
    #[inline(always)]
    fn contract_get<A: HasActions>(
        ctx: &impl HasReceiveContext<()>,
        logger: &mut impl HasLogger,
        state: &mut State,
    ) -> Result<A, ReceiveError> {
        let addr: Key = ctx.parameter_cursor().get()?;
        logger.log(&Event::GET(ctx.invoker(), addr.address))?;
        let v:Info = match ctx.metadata().get_state(addr){
            Ok(v) =>   v,
            Err(_) => return Ok(A::accept())
        };

        ctx.result(v);
        Ok(A::accept())
    }

    #[receive(contract = "Storage", name = "set", parameter = "Info", enable_logger)]
    #[inline(always)]
    fn contract_set<A: HasActions>(
        ctx: &impl HasReceiveContext<()>,
        logger: &mut impl HasLogger,
        state: &mut State,
    ) -> Result<A, ReceiveError> {
        let value: Info = ctx.parameter_cursor().get()?;
        logger.log(&Event::SET(ctx.invoker(), value.storage.clone()))?;
        let _ = ctx.metadata().set_state(ctx.invoker(),value);
        

        Ok(A::accept())
    }

```

## 参数结构

首先我们定义一下，需要使用到的参数结构；

* 存储参数
  我们只需要把需要存储的数据，按照rust语法写出就可以。我们定义一个`u64`的`id`， 两个`String`类型的`name`和`storage`。
  ```
    #[derive(Serialize, SchemaType)]
    struct Info {
        id:         u64,
        name:       String,
        storage:    String,
    }

  ```
  其中`#[derive(Serialize, SchemaType)]`是必不可少的，这个宏会帮助我们生成abi，解析参数。
  
* 读取参数
  读取参数很简单，就是账户地址。
  ```
    #[derive(Serialize, SchemaType)]
    struct Key {
        address : AccountAddress,
    }
  ```
  `AccountAddress`是内置的类型，需要注意。

## 初始化函数

就是用来初始化合约，这个函数只会在合约安装的时候，调用一次。实现如下：

```
    #[init(contract = "Storage", payable)]
    #[inline(always)]
    fn contract_init<I: HasInitContext<()>>(
        ctx: &I,
        amount: Amount,
    ) -> InitResult<State> {
        let state = State {
            height:ctx.metadata().height(),
            time:ctx.metadata().slot_time(),
            owner: ctx.init_origin(),
        };
        Ok(state)
    }
```

`#[init(contract = "Storage", payable)]`宏标识该函数为初始化函数；其中`contract = "Storage"`标识合约的名称，`payable`表明该函数可以接受代币。
函数内部实现功能，获取了链上一些数据，例如高度、时间等；

## 调用函数

对比初始化函数，调用函数可以调用多次，我们以`set`函数为例。

```
    #[receive(contract = "Storage", name = "set", parameter = "Info", enable_logger)]
    #[inline(always)]
    fn contract_set<A: HasActions>(
        ctx: &impl HasReceiveContext<()>,
        logger: &mut impl HasLogger,
        state: &mut State,
    ) -> Result<A, ReceiveError> {
        let value: Info = ctx.parameter_cursor().get()?;
        logger.log(&Event::SET(ctx.invoker(), value.storage.clone()))?;
        let _ = ctx.metadata().set_state(ctx.invoker(),value);
        

        Ok(A::accept())
    }
```

`#[receive(contract = "Storage", name = "set", parameter = "Info", enable_logger)]`宏标识该函数为调用函数；其中`contract = "Storage"`标识合约的名称，`name = "set"`标识该函数的名称，`parameter = "Info"`标识该函数的参数，`enable_logger`表明该函数会使用事件。
函数功能很简单，就是解析参数，获取`Info`类型的参数`vlaue`，然后触发了`event`————`Event::SET`;最终通过`set_state`函数存入链上。