# 命令行交互控制台

## Cmd 介绍

Geno 的命令行工具（CLI）旨在提供一个简洁、高效的方式来管理和与区块链网络进行交互。通过命令行，开发者可以执行各种操作，如创建钱包。本文档将详细介绍这些命令及其使用方法。

## 安装与配置

### 安装条件

1. 确保已安装 Rust（v1.79 ++）执行环境
2. 安装 Git 用于从 GitHub 克隆项目。

### 安装CLI

1. 从 GitHub 克隆仓库
   
   ```shell
   git clone <project-url>
   ```
2. 进入项目 `root` 目录，执行命令安装依赖并生成可执行命令文件

   ```shell
   cargo build
   ```

3. 检查是否安装成功
   
   进入目录 `target\debug\`，执行命令:
   
   ```shell
      geno --version
   ```

### 配置CLI


## 基本命令

### Check Address

   校验地址格式

   ```shell
      geno --check-address <address>
   ```

   + `address` : 地址
  
### Check Keystore

   校验 Keystore 文件

   ```shell
      geno --check-keystore <keystore> <password>
   ```

   + `keystore` : keystore 文件路径
   + `password` : 密码

### Check Signed Data

   验证签名

   ```shell
      geno --check-signed-data <blob_data> <signed_data> <public_key> <algorithm>
   ```

   + `blob_data` : 源数据
   + `signed_data` : 加密后的数据
   + `public_key` : keystore 文件路径
   + `algorithm` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

### Create Account

   创建钱包

   ```shell
      geno --create-account <algorithm_name>
   ```

   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

### Create Keystore

   创建钱包 Keystore

   ```shell
      geno --create-keystore <algorithm_name> <password> 
   ```

   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`
   + `password` : 密码

### Create Keystore From PrivateKey

   根据 Private Key 恢复 Keystore 文件

   ```shell
      geno --create-keystore-from-privatekey <private_key> <algorithm_name> <password>
   ```

   + `private_key` : 私钥
   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`
   + `password` : 密码

### Reset Block Number

   重置区块高度

   ```shell
      geno --force-block-number <number> 
   ```

   + `number` : 区块高度

### Get Address （Private Key）

   根据私钥恢复地址

   ```shell
      geno --get-address <private_key> <algorithm_name> 
   ```

   + `private_key` : 私钥
   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

### Get Address （Public Key）

   根据公钥恢复地址

   ```shell
      geno --get-address-from-pubkey <public_key> <algorithm_name>
   ```

   + `public_key` : 公钥
   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

### Get Address （Keystore）

   根据 Keystore 文件恢复地址

   ```shell
      geno --get-privatekey-from-keystore <keystore> <password>
   ```

   + `keystore` : keystore 文件路径
   + `password` : 密码

### Issue Credential

   发布证书

   ```shell
      geno --issue-credential <issuer_did> <subject_did> <issuer_private_key>
   ```

   + `issuer_did` : 发布者地址
   + `subject_did` : 发布内容
   + `issuer_private_key` : 私钥

### Sign

   签名

   ```shell
      geno --sign-data <private_key> <blob_data> <algorithm_name>
   ```

   + `private_key` : 私钥
   + `blob_data` : 源数据
   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

### Sign (Keystore)

   根据 Keystore 文件进行签名

   ```shell
      geno --sign-data-with-keystore <keystore> <password> <blob_data> <algorithm_name>
   ```

   + `keystore` : keystore 文件路径
   + `password` : 密码
   + `blob_data` : 源数据
   + `algorithm_name` : 加密算法类型: `eddsa_ed25519` or `secp256k1`

## 其他命令

### Get Help

查看帮助

   ```shell
      geno --help
   ```