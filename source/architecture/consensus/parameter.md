# 共识参数可配置项

PBFT可以通过在配置块中的`consensus`字段配置相关参数

proposal_commit_interval_ms  出块时间间隔，如 10000 
proposal_max_tx_size 每个块最大打包交易数量，如 10000
proposal_max_contract_size 每个块最大打包合约交易数量，如 2500

```
[consensus]
proposal_commit_interval_ms = 10000
proposal_max_tx_size = 10000
proposal_max_contract_size = 2500
```

