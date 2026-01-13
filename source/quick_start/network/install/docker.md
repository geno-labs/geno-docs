# Docker/Kubernetes 部署方案

# GenoChain Docker部署

## 部署要求
已安装docker和docker-compose

## 部署方式
1. 镜像源码部署，可自动生成配置文件并分发到对应服务器目录下
   - 自动生成配置文件auto_config目录结构如下：
     - auto_conf.sh：根据ip_list.txt文件中节点ip生成对应的配置文件，也可根据同一IP生成4个不同配置文件，存放在当前文件夹下，链配置信息存储在当前文件夹下的key.txt中。如果可执行文件geno不在上一层目录，则需将../geno改成实际geno存放路径
     - conf_scp.sh：将各配置文件分发到目标服务器目录下
     - ip_list.txt：存放IP地址
2. 镜像部署挂载配置文件路径，需手动配置配置文件，启动docker时挂载配置文件

## 操作步骤

### 方式一：镜像源码部署
1. 从gitlab上下载镜像源码：http://gitlab.geno.io/blockchain/geno-image/-/archive/releasev1.0/geno-image-release-v1.0.zip
2. 在其中一台机器上进入auto_config目录，根据实际情况修改ip_list.txt
3. 执行`./auto_conf.sh`以及`./conf_scp.sh`
4. 构建docker镜像

```bash
docker build -t genochain .

```

5. 各节点服务器启动容器（以配置文件放在 /root 为例）

```bash
docker run -d --name blockchain \
  -v genochain:/etc/genochain \
  -v /root/config.toml:/etc/genochain/configs/config.toml \
  -p 9333:9333 \
  -p 9444:9444 \
  -p 8080:8080 \
  genochain

```

方式二：直接拉取镜像 + 手动挂载配置文件

1. 拉取官方镜像 

```bash
docker pull crpi-015avw673pkoh7zy.cn-beijing.personal.cr.aliyuncs.com/geno-chain/geno-chain:v1.0

```

2. 将已配置好的 config.toml 放在服务器某个目录（例如 /root）

3. 各节点启动容器（示例路径为 /root）  

```bash
docker run -d --name blockchain \
  -v genochain:/etc/genochain \
  -v /root/config.toml:/etc/genochain/configs/config.toml \
  -p 9333:9333 \
  -p 9444:9444 \
  -p 8080:8080 \
  crpi-015avw673pkoh7zy.cn-beijing.personal.cr.aliyuncs.com/geno-chain/geno-chain:v1.0

```

### 方式二：镜像部署挂载配置文件路径

1. 下载镜像

```bash
docker pull crpi-015avw673pkoh7zy.cn-beijing.personal.cr.aliyuncs.com/geno-chain/geno-chain:v1.0

```

2. 将配置好的配置文件放在某目录下

3. 各节点服务器执行以下命令启动容器（配置文件存放目录以/root为例）：

```bash
docker run -d --name blockchain -v genochain:/etc/genochain -v /root/config.toml:/etc/genochain/configs/config.toml -p 9333:9333 -p 9444:9444 -p 8080:8080 crpi-015avw673pkoh7zy.cn-beijing.personal.cr.aliyuncs.com/geno-chain/geno-chain:v1.0
```

## 单服务器部署

1. 通过docker-compose在同一服务器部署多个节点，docker-compose.yml配置如下：

```bash
version: '3'

services:
  node1:
    image: geno-chain:v1.0  # 根据镜像名修改
    container_name: blockchain-node1
    restart: always
    ports:
      - "9333:9333"  # 根据配置文件修改
      - "9444:9444"  
      - "8080:8080"  
    volumes:
      - genochain-node1:/etc/genochain 
      - /root/config_node0.toml:/etc/genochain/configs/config.toml  # 根据配置文件名修改

  node2:
    image: geno-chain:v1.0  # 根据镜像名修改
    container_name: blockchain-node2
    restart: always
    ports:
      - "9334:9334"   # 根据配置文件修改
      - "9445:9445"
      - "8081:8080"
    volumes:
      - genochain-node2:/etc/genochain
      - /root/config_node1.toml:/etc/genochain/configs/config.toml   # 根据配置文件名修改

  node3:
    image: geno-chain:v1.0   # 根据镜像名修改
    container_name: blockchain-node3
    restart: always
    ports:
      - "9335:9335"   # 根据配置文件修改
      - "9446:9446"
      - "8082:8080"
    volumes:
      - genochain-node3:/etc/genochain
      - /root/config_node2.toml:/etc/genochain/configs/config.toml   # 根据配置文件名修改

  node4:
    image: geno-chain:v1.0   # 根据镜像名修改
    container_name: blockchain-node4
    restart: always
    ports:
      - "9336:9336"   # 根据配置文件修改
      - "9447:9447"
      - "8083:8080"
    volumes:
      - genochain-node4:/etc/genochain
      - /root/config_node3.toml:/etc/genochain/configs/config.toml   # 根据配置文件名修改

volumes:
  genochain-node1:
  genochain-node2:
  genochain-node3:
  genochain-node4: 
```

2. 启动容器


```bash
docker-compose up -d
```

## 其他

Dockerfile配置如下：

```bash
# 使用基础镜像，例如 Ubuntu
FROM ubuntu:20.04

# 创建应用目录
RUN mkdir -p /etc/genochain/configs

# 复制你的可执行文件和配置文件到镜像中
COPY ./geno /etc/genochain/geno
COPY ./configs/config.toml /etc/genochain/configs/config.toml
COPY ./configs/log_filter.txt /etc/genochain/configs/log_filter.txt

# 设置工作目录
WORKDIR /etc/genochain/

# 设置权限
RUN chmod +x ./geno

# 运行区块链应用
CMD ["./geno"]
```