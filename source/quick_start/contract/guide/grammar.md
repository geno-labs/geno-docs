# 常用类型、方法与库函数

## 库函数

用户可以通过库函数，访问某些区块链执行上下文中的信息。例如，区块时间，合约地址等。访问这些库函数的时候一般都是通过，固定的参数`ctx: &impl HasReceiveContext<()>,`，所以我们以下示例中，都是使用`ctx`代表这个合约上下。

* **区块时间**  
  获取当前区块的事件,返回值是`Timestamp`.
  ```rust
  fn slot_time(&self) -> SlotTime
  //示例
  let time = ctx.metadata().slot_time()；
  ```
* **区块高度**  
  返回当前区块高度
  ```rust
  fn height(&self) -> u64
  //示例
  let height = ctx.metadata().height()；
  ```
* **交易hash**  
  返回当前交易的hash
  ```rust
  fn tx_hash(&self)->String
  //示例
  let tx_hash = ctx.metadata().tx_hash()；
  ```
* **获取调用参数**  
  在函数中使用，返回当前函数的参数，返回值是泛型。
  ```rust
  fn parameter_cursor(&self) -> Self::ParamType
    //示例
  let addr = ctx.parameter_cursor().get()?;
  ```
* **创建合约账户**  
  只在初始化函数中存在，返回发起交易的地址。
  ```rust
  fn init_origin(&self) -> AccountAddress;
    //示例
  let creator = ctx.init_origin();  
  ```
* **合约所有者**  
  返回合约拥有者的地址
  ```rust
  fn owner(&self) -> AccountAddress;
    //示例
  let owner = ctx.owner();  
  ```
* **合约地址**  
  返回合约的地址
  ```rust
  fn self_address(&self) -> AccountAddress;
    //示例
  let addr = ctx.self_address();  
  ```
* **合约余额**  
  返回合约的余额
  ```rust
  fn self_balance(&self) -> Amount;
    //示例
  let balance = ctx.self_balance();  
  ```
* **合约调用者**  
  返回调用合约的地址
  ```rust
  fn invoker(&self) -> AccountAddress;
    //示例
  let user = ctx.invoker();  
  ```
* **合约返回值**  
  合约返回值，参数是一个泛型，这个参数是标记`#[derive(Serialize, SchemaType)]`的数据结构
  ```rust
  fn result<A:Serial>(&self, data:A);
    //示例
  ctx.result();  
  ```
* **合约事件**  
  触发事件，事件会标记`#[contract_event(contract = "Storage")]`的数据结构
  ```rust
  fn log<S: Serial>(&mut self, event: &S) -> Result<(), LogError>
    //示例
  logger.log(&Event::Transfer(owner_address, receiver_address, amount))?;  
  ```
* **存储数据**  
  存储到链上的数据
  ```rust
  fn set_state<K:Serial, V:Serial>(&self, key:K, value:V)->Result<(), &str>;
    //示例
  let _ = ctx.metadata().set_state(key, value);  
  ```
* **读取数据**  
  从链上读取数据
  ```rust
  fn get_state<K:Serial, V:Deserial>(&self, key:K)->Result<V, &str>;
    //示例
  let info = ctx.metadata().get_state(key);  
  ```
* **转账&调用合约**  
  合约内进行转账与调用合约的接口，转账的时候，不需要填写`receive_name`,`parameter`;
  ```rust
  fn invoke<K:Serial, V:Deserial>(
        &self,
        ca: &AccountAddress,
        receive_name: String,
        amount: Amount,
        parameter: K,
    ) -> Result<V,&str>;
    //示例
  let r = match ctx.invoke::<Request, InitParams>(
                &contract_addres,
                function,
                Amount::zero(),
                a
            );  
  ```

## 常用类型与方法
* **常用声明宏**  
  - 根据条件判断是否返回错误；  
    ```rust
    ensure!()
      //示例
    ensure!(sender_balance >= amount, ReceiveError::InsufficientFunds); 
    ```
  - 返回错误，请提前定制错误类型；  
    ```rust
    bail!()
      //示例
    bail!(ReceiveError::ErrSetData);  
    ```

* **常用过程宏**
  - 合约状态宏  
    用于标记合约状态
    ```rust
    #[contract_state(contract="Storage")]
    ```
  - 合约初始化方法宏  
    标记合约的初始化函数
    ```rust
    #[init(contract="Storage")]
    ```
  - 合约调用方法宏
    标记合约的调用函数
    ```rust
    #[receive(contract="Storage",name="get")]
    ```
  - 交互数据  
    标记合约函数的参数，例如输入参数，返回值。
    ```rust
    #[derive(SchemaType)]
    ```
  - 事件数据  
    标记事件结构体
    ```rust
    #[contract_event(contract = "Storage")]
    ```


* **常用类型**  
  由于合约以Rust为宿主语言，所以你可以使用Rust支持的所有类型。但是为了方便编写合约，我也提供了一些内置的数据类型。
  - Amount  
    表示链上代币的数量，实际类型是`u64`。  
  - Timestamp  
    ```rust
    pub struct Timestamp {
    // Milliseconds since unix epoch.
        pub(crate) milliseconds: u64,
    }
    ```
    表示时间————自unix纪元以来的毫秒，实际类型是`u64`。   
  - AccountAddress  
    ```rust
    pub struct AccountAddress(pub [u8; ACCOUNT_ADDRESS_SIZE]);
    ```
    表示账户地址，长度为42的`u8`数组；
  - Duration  
    ```rust
    pub struct Duration {
        pub(crate) milliseconds: u64,
    }
    ```
    以毫秒为单位的时间长度，实际类型是`u64`。


  