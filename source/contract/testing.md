# 合约开发与测试

我们将编写一个简单的合约为例，介绍合约开发步骤，涵盖准备工作，开发，构建，部署以及调用等。

## 准备工作
在终端中执行以下命令创建智能合约项目：
```bash
cargo new --lib helloworld
```
上述命令将会在当前目录下创建一个名为 helloworld 的智能合约项目，并新建了一个名为“helloworld”的目录：
```
helloworld/
├── .git
├── Cargo.toml
└── src
│   └──lib.rs
```
* **Cargo.toml**：项目配置清单，主要包括项目信息、外部库依赖、编译配置等，一般而言无需修改该文件，除非有特殊的需求（如引用额外的第三方库、调整优化等级等）；
* **src/lib.rs**：智能合约项目根文件，合约代码存放于此文件中。智能合约项目创建完毕后，lib.rs文件中会自动填充部分样板代码，我们可以基于这些样板代码做进一步的开发。

## 开发合约
我们将以下代码复制到`lib.rs`中。
```rust
#![cfg_attr(not(feature = "std"), no_std)]
use c_std::{collections::*, *};

#[contract_state(contract = "helloworld")]
#[derive(Serialize, SchemaType)]
pub struct State {
    init: u64,
}

#[init(contract = "helloworld")]
#[inline(always)]
fn contract_init(ctx: &impl HasInitContext) -> InitResult<State> {
    let state = State { init: 0 };
    Ok(state)
}

#[derive(Debug, PartialEq, Eq, Reject)]
enum ReceiveError {
    ParseParams,
    OnlyAccounts,
    MoreThanAllowed,
    LogFull,
    InsufficientFunds,
    LogMalformed
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
    fn from(_: ParseError) -> Self {
        ReceiveError::ParseParams
    }
}

#[derive(Serialize, SchemaType)]
struct Information {
    name: String,
}

#[receive(contract = "helloworld", name = "hello", result = "Information")]
#[inline(always)]
fn get_info<A: HasActions>(
    ctx: &impl HasReceiveContext<()>,
    _state: &mut State,
) -> Result<A, ReceiveError> {

    ctx.result(Information {
        name: "hello world!".to_string(),
    });
    Ok(A::accept())
}

```

修改`Cargo.toml`文件
```toml
[package]
name = "helloworld"
version = "0.1.0"
edition = "2018"

[features]
default = ["std"]
std = ["c-std/std"]

[dependencies]
c = {path = "../c-rust-smart-contracts/c-std", default-features = false}

[lib]
crate-type=["cdylib", "rlib"]

[profile.release]
opt-level = "s"
```

## 编译合约
在 `helloworld` 项目根目录下执行以下命令即可开始进行构建

```bash
cargo build --target wasm32-unknown-unknown --release --target-dir target/c --features g-std/build-schema 
```
命令执行完成后，会显示如下形式的内容：
```bash
    Finished release [optimized] target(s) in 20.88s
```
然后可以使用`wasm-stripe` 工具，对合约进行优化。
```bash
wasm-stripe  target\c\wasm32-unknown-unknown\release\helloworld.wasm
```

## 部署节点
启动节点参照[节点启动](https://geno-docs.readthedocs.io/zh-cn/latest/node/install/signle_node.html)

## 调用与测试
参照这里[API](https://geno-docs.readthedocs.io/zh-cn/latest/sdk/json_rpc/index.html)  
部署合约，将编译好的合约，拷贝到相应的位置。
```python
code = api.read_code_from_file("./contract/helloworld.wasm")

api.call(
    from_address = api.genesis_address, 
    from_priv = api.genesis_private_key, 
    name = "helloworld", 
    nonce = nonce,
    code = code,
    balance = 0)
```
部署成功后，通过查询交易哈希，可以找到新合约的地址`did:geno:0x17h81daf6217e495fae32286c3137b6e0f78cdfd`；
```json
{'chain_id': '2024', 'jsonrpc': '2.0', 'id': 1, 'result': {'call_data': {'code': 0, 'events': [], 'gas_used': 0, 'message': '', 'result': ''}, 'chain_id': '2024', 'err_code': 0, 'err_msg': '{"contract_address":"did:geno:0x17h81daf6217e495fae32286c3137b6e0f78cdfd"}', 'gas_limit': 20000, 'gas_price': '1', 'hash': '8018faff89acb1af4437387235ed4aab6051ed63eb5f01c2a5d5270f4f1406ce', 'height': 79, 'kind': 2, 'nonce': 1, 'payload': {'Call': {'balance': 0, 'ctype': 0, 'input': {'code': 'AGFzbQEAAAABZBBAxAAAQAAAA==', 'function': '', 'name': 'helloworld', 'parameter': '\n\n'}, 'to': ''}}, 'sender': 'did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1', 'timestamp': 1736414742275}, 'error': {'code': 0, 'message': 'Success', 'data': None}}
```

调用合约，
```python
api.call(
    from_address = api.genesis_address, 
    from_priv = api.genesis_private_key, 
    function = "hello",
    to = contract_address,
    nonce = nonce)
```
调用成功后，通过交易哈希，可以查询到返回值`hello world!`.
```json
{'chain_id': '2024', 'jsonrpc': '2.0', 'id': 1, 'result': {'call_data': {'code': 0, 'events': [], 'gas_used': 0, 'message': '', 'result': ''}, 'chain_id': '2024', 'err_code': 0, 'err_msg': '{\n  "name": "hello world!"\n}', 'gas_limit': 20000, 'gas_price': '1', 'hash': '8268c83450493f01127aa89ad4d3d9a6ae46f8373759b5ad14ce713e7297ac94', 'height': 98, 'kind': 2, 'nonce': 2, 'payload': {'Call': {'balance': 0, 'ctype': 0, 'input': {'code': '', 'function': 'hello', 'name': '', 'parameter': '\n\n'}, 'to': 'did:geno:0x26581daf6217f495fae32286c3137b6e0f78adf8'}}, 'sender': 'did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1', 'timestamp': 1736414932301}, 'error': {'code': 0, 'message': 'Success', 'data': None}}
```
