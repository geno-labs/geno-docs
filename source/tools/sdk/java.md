# JAVA SDK使用说明

## SDK 介绍

区块链技术以其去中心化、安全性和透明度著称，在金融、供应链管理、医疗等多个领域有着广泛的应用。Java，作为一种广泛使用的编程语言，因其跨平台能力和丰富的生态系统，成为区块链开发的首选语言之一。Java SDK为开发者提供了与区块链网络交互的一套工具和接口，使得在Java环境中开发区块链应用变得更加便捷。

主要功能包括：

+ 交易管理：创建、签名和发送交易到区块链网络
+ 智能合约交互：部署、调用和管理智能合约
+ 账户管理：生成和管理公钥、私钥以及地址
+ 区块和交易查询：从区块链网络获取区块和交易信息

## 安装与配置

### 环境要求

+ Java版本：要求Java 17或更高版本。
+ Maven：用于项目构建和依赖管理。


### 安装步骤

1. 从GitHub或其他源代码管理平台克隆项目

2. 使用Maven下载必要的依赖库

    ```shell
        mvn clean install
    ```

### 配置SDK

1. 配置节点 URL

    ```java
        WebClient client = WebClient.build(new HttpService(url));
    ```


## 基本使用指南

### 钱包类

+ 创建钱包公私钥对
  
    ```java
        Wallet.create();
    ```

+ 根据私钥恢复钱包
  
    ```java
        String privateKey = "xxxx";
        Wallet.recover(privateKey);
    ```

### 账户类

+ 查询 Nonce
  
    ```java
        WebClient client = WebClient.build(new HttpService(url));
        String address = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1";
        Long nonce = Account.getNonce(client, address);
    ```

+ 查询账户余额
  
    ```java
        WebClient client = WebClient.build(new HttpService(url));
        String address = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1";
        Account.getBalance(client, address)
    ```

+ 查询账户信息
  
    ```java
        WebClient client = WebClient.build(new HttpService(url));
        String address = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1";
        Account.getAccount(client, address);
    ```

### 交易类

+ 生成交易 Hash
  
    ```java
        String from = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1";
        String to = "did:geno:0x2c719b29d42974695c106debacd3974950950fdf";
        Long nonce = 1L;
        Long amount = 1L;
        TransferTransaction transaction = TransferTransaction.build(from, nonce, to, amount);
        String hash = TransactionManage.txHash(transaction);
    ```

+ 签名交易
  
    ```java
        String from = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1";
        String to = "did:geno:0x2c719b29d42974695c106debacd3974950950fdf";
        Long nonce = 1L;
        Long amount = 1L;
        TransferTransaction transaction = TransferTransaction.build(from, nonce, to, amount);
        String raw = TransactionManage.sign(privateKey, transaction);
    ```

+ 提交交易
  
    ```java
        String raw = "0a84010a336469643a67656e6f3a30786636623032613264343762383465383435623765333632333335356630343162636233366461663110031a043230323420012a3b0a390a336469643a67656e6f3a30783263373139623239643432393734363935633130366465626163643339373439353039353066646610809f493088a4013a02323012730a2002cf651570988a1c474384c2257aaa0cb07417ae5cd0b25a638abad39c4061bf124018b9b1f3d29f97dd9ebfe6612b12b912cb49c938ae53034c0b1704f35f089f8b398c5243388b247d3f50001869430583b7006e1d784f12ac657505f8c1e6660b1a0d65646473615f656432353531391a42307861653537353131393133646330356231383234383666306238393235653635633964343533333038373930316336333734616161303964313966346437396437";
        String hash = Transaction.sendRaw(client, raw);
    ```


### 智能合约类

+ 部署合约

    ```java
        String hash = null;
        try {
            //the type of 'call' transaction
            Long currentNonce = Account.getNonce(client, from);
            Long nonce = currentNonce + 1L; // nonce 值

            //set 'deploy contract' type parameters
            Long amount = 1L;
            String contractName = "contractDefault-stm";
            URL urlContract = this.getClass().getClassLoader().getResource("contracts/contractDefault_stm.wasm");
            byte[] contractCode = Files.readBytes(new File(urlContract.getPath()));
            String parameter = ""; //合约参数，非必填

            CallTransaction transaction = CallTransaction.buildDeployContract(from, nonce, amount, contractName, contractCode, parameter);
            hash = Transaction.send(client, privateKey, transaction);
        } catch (IOException e) {
            e.printStackTrace();
        }
    ```

+ 调用合约

    ```java
        String hash = null;
        try {
            //the type of 'call' transaction
            Long currentNonce = Account.getNonce(client, from);
            Long nonce = currentNonce + 1L; // nonce 值

            //set 'deploy contract' type parameters
            Long amount = null;
            String to = "did:geno:0xc8ebf548efe3eb801e52014abadd25213918417b"; // 合约地址，必填
            String function = "set"; // 调用函数名，必填
            JSONObject parameterJson = new JSONObject();
            parameterJson.put("key", "test-call");
            parameterJson.put("value", "call-contract");
            String parameter = parameterJson.toJSONString(); // 合约参数，非必填

            CallTransaction transaction = CallTransaction.buildCallContract(from, nonce, to, amount, function, parameter);
            hash = Transaction.send(client, privateKey, transaction);
        } catch (IOException e) {
            e.printStackTrace();
        }
    ```

+ 升级合约

    ```java
        String hash = null;
        try {
            //the type of 'call' transaction
            Long currentNonce = Account.getNonce(client, from);
            Long nonce = currentNonce + 1L; // nonce 值

            //set 'upgrade contract' type parameters
            String contractName = "contractDefault-stm"; //合约名称，必填
            String to = "did:geno:0xc8ebf548efe3eb801e52014abadd25213918417b"; //合约地址，必填
            URL urlContract = this.getClass().getClassLoader().getResource("contracts/contractDefault_stm.wasm");
            byte[] contractCode = Files.readBytes(new File(urlContract.getPath())); //合约字节码，必填

            CallTransaction transaction = CallTransaction.buildUpgradeContract(from, nonce, to, contractCode, contractName);
            hash = Transaction.send(client, privateKey, transaction);
        } catch (IOException e) {
            e.printStackTrace();
        }
    ```

+ 查询合约

    ```java
        try {
            String ca = "did:geno:0xc8ebf548efe3eb801e52014abadd25213918417b"; //合约地址，必填
            String function = "get"; //合约函数名，必填
            JSONObject parameterJson = new JSONObject();
            parameterJson.put("key", "test-call");
            String parameter = parameterJson.toJSONString(); //合约参数，非必填
            String a = "{\"key\":\"test-call\"}";
            CallDataInfo callDataInfo = Contract.call(client, ca, function, parameter);
        } catch (IOException e) {
            e.printStackTrace();
        }
    ```

## 高级特性

+ 事件监听：可以设置事件监听器以实时接收区块链事件，如新区块产生、交易确认等。
+ 多签名钱包：支持创建和管理多签名账户，提高安全性。
+ 数据加密：提供数据加密和解密的API，确保数据在传输和存储时的安全性。

## 常见问题及解决方案

+ 连接问题：确保网络配置正确，节点可访问，且证书配置正确。
+ 交易失败：检查交易是否有足够的gas，私钥是否正确，网络是否拥堵。
+ 合约部署延迟：确认节点同步状态和网络延迟情况。

## 开发资源

+ 官方文档：详见项目GitHub页面或官方网站。
+ 社区支持：通过GitHub Issues或相关开发者社区获取帮助，如Stack Overflow。
+ 示例代码：项目仓库中包含多个使用示例，帮助开发者快速上手。