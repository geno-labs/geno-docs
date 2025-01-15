# 系统合约编写

#### 定义合约版本

```
const EXAMPLE_VERSION: u64 = 1000;
```



#### 定义合约

```
pub struct SystemContractExample {
    pub context: ContractSystemContext,
}
```

合约必须包含上下文成员ContractSystemContext



#### 实现合约统一接口

```
//调用合约自定义方法
fn call(
        &mut self,
        method_name: &str,
        args: String,
        context: &ContractSystemContext,
        world_state: WorldState,
    ) -> TransactionResult {
        let mut container = HashMap::new();
        //注册所有合约的自定义方法
        SystemContractExample::register_methods(&mut container);
        //更新合约上下文
        self.update_context(context);
        //根据method_name参数分发接口（调用method_name接口）
        self.dispatch(method_name, args, world_state, &container)
    }
    
//返回当前合约上下文    
fn context(&self) -> ContractSystemContext {
        self.context.clone()
    }
   
//更新合约上下文   
fn update_context(&mut self, context: &ContractSystemContext) {
        self.context.invoker_address = context.invoker_address();
        self.context.transaction_hash = context.transaction_hash();
        self.context.block_timestamp = context.block_timestamp();
        self.context.block_height = context.block_height();
        self.context.itself_account = context.itself();
        self.context.invoker_account = context.invoker();
        self.context.validators = context.validators();
        self.context.asset_committee = context.asset_committee();
    }  

//重置合约上下文    
fn reset_context(&mut self) {
        self.context.invoker_address = "".to_string();
        self.context.transaction_hash = "".to_string();
        self.context.block_timestamp = 0;
        self.context.block_height = 0;
        self.context.itself_account = Arc::new(RwLock::new(AccountObject::default()));
        self.context.invoker_account = Arc::new(RwLock::new(AccountObject::default()));
        self.context.validators = vec![];
        self.context.asset_committee = vec![];
    }
```

合约统一接口所有代码是每个合约必须实现的，代码统一，不必更改。



#### 实现合约自定义接口

实现合约的自定义的业务逻辑

```
#[register_methods]   //必须标注register_methods
impl SystemContractExample {
	//所有参数都通过json解析
    pub fn foo(&mut self, args: String, world_state: WorldState) -> TransactionResult {
        println!("foo args: {} height: {}", args, self.context.block_height());
        TransactionResult::default()
    }
}
```

