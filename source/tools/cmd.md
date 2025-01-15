# 命令行交互控制台

## Cmd 介绍

Geno 的命令行工具（CLI）旨在提供一个简洁、高效的方式来管理和与区块链网络进行交互。通过命令行，开发者可以执行各种操作，如创建钱包、发送交易、部署智能合约等。本文档将详细介绍这些命令及其使用方法。

## 安装与配置

### 安装条件

1. 确保已安装Node.js（如果使用基于Node.js的CLI）或者其他支持的环境，如Python或Go。
2. 安装Git用于从GitHub克隆项目。

### 安装CLI

1. 从GitHub克隆仓库
   
   ```shell
   git clone <project-url>
   ```
2. 安装依赖（以Node.js为例）
   ```shell
   cd geno-cli
   npm install
   ```
### 配置CLI

+ 在项目根目录中创建或编辑配置文件（如config.json）来指定节点地址、网络ID等。

## 基本命令

### 创建钱包

```shell
geno-cli get-block <block-number-or-hash>
```

### 查看钱包余额

```shell
geno-cli get-block <block-number-or-hash>
```

### 发送交易

```shell
geno-cli send-tx --from <from-address> --to <to-address> --amount <value>
```

### 部署智能合约

```shell
geno-cli deploy-contract --file <path-to-contract> --abi <path-to-abi>
```

### 调用合约方法

```shell
geno-cli call-contract --address <contract-address> --method <method-name> --params <comma-separated-params>
```

## 高级命令

### 查询区块信息

```shell
geno-cli get-block <block-number-or-hash>
```

### 查询交易信息

```shell
geno-cli get-tx <transaction-hash>
```