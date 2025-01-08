# 配置文件结构说明

#### 节点身份配置

```
network_id = 8108773  #网路ID
chain_id = "2024"     #链ID
node_address = "did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e" #节点地址
node_private_key = "43f47b5387b5321a712c5960074576b114835aa8e1ce1c2e6ab070d7ffb44346"  #节点私钥
```



#### 共识配置

```
[consensus]
proposal_commit_interval_ms = 10000  #每轮共识时间间隔
proposal_max_tx_size = 10000		 #每次共识打包交易最大数量
proposal_max_contract_size = 2500    #每次共识打包合约交易最大数量
```



#### 数据库配置

```
[db]
key_vaule_max_open_files = 1000    
kv_db_path = "./data/kv.db"           #其他信息存储数据库
block_db_path = "./data/block.db"     #区块交易相关信息存储数据库
state_db_path = "./data/state.db"	  #账户相关信息存储数据库
```



#### 创世区块配置

```
[genesis_block]
genesis_account = "did:geno:0xf6b02a2d47b84e845b7e3623355f041bcb36daf1"  #创世账号
validators = ["did:geno:0x160b54be617f4bff07bd6c994fc6dd17a69d5e4e"]     #验证节点账号
system_contract_manager ="did:geno:0x109e45bbc6b3c29012f23e7881a0f6209fb34325" #系统合约管理人
asset_committee=["did:geno:0xed1bad1e68288932eba3fe495949ec76d41533ab","did:geno:0xb10bad2e5eb7fa380dd00ac782549f9d2abeb8dc","did:geno:0x99f56d55790c5095d23ac3a02aca47e840acb569"]												#资产委员会，用来选举资产发布者
asset_issuer="did:geno:0x052c4dc8f39a0dcf0024e06499cde38eb4cbdf99"				#默认初始资产发布者

```



#### jsonrpc配置

```
[json_rpc]
address = "0.0.0.0:8081"		#jsonrpc默认端口
batch_size_limit = 20
page_size_limit = 1000
content_length_limit = 1048576  #提交内容大小限制
```



#### p2p网络配置

```
[p2p]
codec_type = "default"    #编码方式默认
local_addr = "192.168.1.118:8133"  #本节点的地址和端口
heartbeat_interval = 60            #心跳检查
target_peer_connection = 50        #最大连接数
max_connection = 2000              
connect_timeout = 5
listen_addr = "0.0.0.0:8133"       #节点p2p网络监听端口
known_peers = ["192.168.1.116:8133","192.168.1.118:8133","192.168.1.117:8133","192.168.1.115:8133"]  #其他节点的地址和端口(交易)
consensus_listen_addr = "0.0.0.0:8140"
consensus_known_peers = ["192.168.1.116:8140","192.168.1.118:8140","192.168.1.117:8140","192.168.1.115:8140"] #其他节点的地址和端口（共识）
```



#### 交易池配置

```
[tx_pool]
capacity = 1_000_000         			#交易池最大容量
capacity_per_user = 10000				#每个账号的最大容量
transaction_timeout_secs = 600			#交易存货时间
transaction_gc_interval_ms = 60_000     #定时清理交易间隔
broadcast_batch_size = 30				#单次广播最大交易数
broadcast_interval_ms = 1000			#单次广播间隔
max_concurrent_number = 10				#交易池并行线程数量
```

