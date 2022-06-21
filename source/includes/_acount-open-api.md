# 账户接口

### 获取当前账户的存款记录

`GET /openapi/v1/depositOrders`

```shell
GET /openapi/v1/depositOrders  (HMAC SHA256)
```

**权重:**
5

**参数:**

| 名称         | 类型     | 是否强制 | 描述                           |
|------------|--------|------|------------------------------|
| token      | STRING | NO   | 默认全部                         |
| startTime  | LONG   | NO   |                              |
| endTime    | LONG   | NO   |                              |
| fromId     | LONG   | NO   | 从哪个OrderId起开始抓取。默认抓取最新的存款记录。 |
| limit      | INT    | NO   | 默认 500; 最大 1000.             |
| recvWindow | LONG   | NO   |                              |
| timestamp  | LONG   | YES  |                              |

**注意:**

* 如果`orderId`设定好了，会筛选订单小于`orderId`的。否则会返回最近的订单信息。

**响应:**

```json
[
  {
	"orderId": 100234,
	"token": "EOS",
	"address": "deposit2bh",
	"addressTag": "19012584",
	"fromAddress": "clarkkent",
	"fromAddressTag": "19029901",
	"time": 1499865549590,
	"quantity": "1.01"
  }
]
```
### 账户提币记录 (USER_DATA)

```shell
GET /openapi/v1/withdrawalOrders  (HMAC SHA256)
```

获取当前账户的提币记录

**权重:** 5

**参数:**

| 名称         | 类型     | 是否强制 | 描述                           |
|------------|--------|------|------------------------------|
| token      | STRING | NO   | 默认全部                         |
| startTime  | LONG   | NO   |                              |
| endTime    | LONG   | NO   |                              |
| fromId     | LONG   | NO   | 从哪个OrderId起开始抓取。默认抓取最新的存款记录。 |
| limit      | INT    | NO   | 默认 500; 最大 1000.             |
| recvWindow | LONG   | NO   |                              |
| timestamp  | LONG   | NO   |                              |

**注意:**

* 如果`orderId`设定好了，会筛选订单小于`orderId`的。否则会返回最近的订单信息。

**响应:**

```json
[
    {
        "time":"1536232111669",
        "orderId":"90161227158286336",
        "accountId":"517256161325920",
        "tokenId":"BHC",
        "tokenName":"BHC",
        "address":"0x815bF1c3cc0f49b8FC66B21A7e48fCb476051209",
        "addressExt":"address tag",
        "quantity":"14", // 提币金额
        "arriveQuantity":"14", // 到账金额
        "statusCode":"PROCESSING_STATUS",
        "status":3,
        "txid":"",
        "txidUrl":"",
        "walletHandleTime":"1536232111669",
        "feeTokenId":"BHC",
        "feeTokenName":"BHC",
        "fee":"0.1",
        "requiredConfirmNum":0, // 要求确认数
        "confirmNum":0, // 确认数
        "kernelId":"", // BEAM 和 GRIN 独有
        "isInternalTransfer": false // 是否内部转账
    },
    {
        "time":"1536053746220",
        "orderId":"762522731831527",
        "accountId":"517256161325920",
        "tokenId":"BHC",
        "tokenName":"BHC",
        "address":"fdfasdfeqfas12323542rgfer54135123",
        "addressExt":"EOS tag",
        "quantity":"",
        "arriveQuantity":"10",
        "statusCode":"BROKER_AUDITING_STATUS",
        "status":"2",
        "txid":"",
        "txidUrl":"",
        "walletHandleTime":"1536232111669",
        "feeTokenId":"BHC",
        "feeTokenName":"BHC",
        "fee":"0.1",
        "requiredConfirmNum":0, // 要求确认数
        "confirmNum":0, // 确认数
        "kernelId":"", // BEAM 和 GRIN 独有
        "isInternalTransfer": false // 是否内部转账
    }
]
```

**提币状态说明**

| status | statusCode                | 描述     |
|:-------|:--------------------------|:-------|
| ~~1~~      | ~~BROKER_AUDITING_STATUS~~    | ~~券商审核中~~  1 转 3|
| ~~2~~      | ~~BROKER_REJECT_STATUS~~      | ~~券商审核拒绝~~ 2 转 4|
| 3      | AUDITING_STATUS           | 平台审核中  |
| 4      | AUDIT_REJECT_STATUS       | 平台审核拒绝 |
| 5      | PROCESSING_STATUS         | 钱包处理中  |
| 6      | WITHDRAWAL_SUCCESS_STATUS | 提币成功   |
| 7      | WITHDRAWAL_FAILURE_STATUS | 提币失败   |
| 8      | BLOCK_MINING_STATUS       | 区块打包中  |


### 获取某个提币记录 (USER_DATA)

```shell
GET /openapi/v1/withdraw/detail  (HMAC SHA256)
```

获取当前账户的提币记录

**权重:** 2

**参数:**

| 名称            | 类型     | 是否强制 | 描述                             |
|---------------|--------|------|--------------------------------|
| orderId       | LONG   | NO   | orderId和clientOrderId两者必须有一个有值 |
| clientOrderId | STRING | NO   | orderId和clientOrderId两者必须有一个有值 |

**响应:**

```json

{
    "time":"1536232111669",
    "orderId":"90161227158286336",
    "accountId":"517256161325920",
    "tokenId":"BHC",
    "tokenName":"BHC",
    "address":"0x815bF1c3cc0f49b8FC66B21A7e48fCb476051209",
    "addressExt":"address tag",
    "quantity":"14", // 提币金额
    "arriveQuantity":"14", // 到账金额
    "statusCode":"PROCESSING_STATUS",
    "status":3,
    "txid":"",
    "txidUrl":"",
    "walletHandleTime":"1536232111669",
    "feeTokenId":"BHC",
    "feeTokenName":"BHC",
    "fee":"0.1",
    "requiredConfirmNum":0, // 要求确认数
    "confirmNum":0, // 确认数
    "kernelId":"", // BEAM 和 GRIN 独有
    "isInternalTransfer": false // 是否内部转账
}
```


### 子账户列表(SUB_ACCOUNT_LIST) 存疑

```shell
POST /openapi/v1/subAccount/query
```

查询子账户列表

**参数:**

无

**权重:**
5

**响应:**

```json
[
    {
        "accountId": "122216245228131",
        "accountName": "",
        "accountType": 1,
        "accountIndex": 0 // 账户index 0 默认账户 >0, 创建的子账户
    },
    {
        "accountId": "482694560475091200",
        "accountName": "createSubAccountByCurl", // 子账户名称
        "accountType": 1, // 子账户类型 1 币币账户 3 合约账户
        "accountIndex": 1
    },
    {
        "accountId": "422446415267060992",
        "accountName": "",
        "accountType": 3,
        "accountIndex": 0
    },
    {
        "accountId": "482711469199298816",
        "accountName": "createSubAccountByCurl",
        "accountType": 3,
        "accountIndex": 1
    }
]
```


### 账户内转账 (ACCOUNT_TRANSFER) 存疑

```shell
POST /openapi/v1/transfer
```

转账

**权重:**
1

**参数:**

| 名称               | 类型     | 是否强制 | 描述                                |
|------------------|--------|------|-----------------------------------|
| fromAccountType  | int    | YES  | 源账户类型, 1 钱包(币币)账户 2 期权账户 3 合约账户   |
| fromAccountIndex | int    | YES  | 子账户index, 主账户Api调用时候有用，从子账户列表接口获取 |
| toAccountType    | int    | YES  | 目标账户类型, 1 钱包(币币)账户 2 期权账户 3 合约账户  |
| toAccountIndex   | int    | YES  | 子账户index, 主账户Api调用时候有用，从子账户列表接口获取 |
| tokenId          | STRING | YES  | tokenID                           |
| amount           | STRING | YES  | 转账数量                              |

**响应:**

```json
{
    "success":"true" // 0成功
}
```

**说明**

1、转账账户和收款账户的其中一方，必须是主账户(钱包账户)

2、主账户Api可以从钱包账户向其他账户(包括子账户)转账，也可以从其他账户向钱包账户转账

3、**子账户Api调用的时候只能从当前子账户向主账户(钱包账户)转账，所以fromAccountType\fromAccountIndex\toAccountType\toAccountIndex不用填**


### 查询流水 (BALANCE_FLOW)

```shell
POST /openapi/v1/balance_flow 存疑
```

查询账户流水

**权重:**
5

**参数:**

| 名称           | 类型      | 是否强制 | 描述                 |
|--------------|---------|------|--------------------|
| accountType  | int     | 否    | 账户对应的account_type  | 默认1 |
| accountIndex | int     | 否    | 账户对应的account_index | 默认0 |
| tokenId      | string  | 否    | token_id           | eg: BTC                                |
| fromFlowId   | long    | 否    | 顺向查询数据             | 指定查询 id < fromFlowId的数据 |
| endFlowId    | long    | 否    | 反向查询数据             | 指定查询 id > |endFlowId的数据  |
| startTime    | long    | 否    | 开始时间               | 毫秒时间戳                             |
| endTime      | long    | 否    | 结束时间               | 毫秒时间戳                             |
| limit        | integer | 否    | 每页记录数              | 默认50，最大100                                       |

**响应:**

```json
[
    {
        "id": "539870570957903104",
        "accountId": "122216245228131",
        "tokenId": "BTC",
        "tokenName": "BTC",
        "flowTypeValue": 51, // 流水类型
        "flowType": "USER_ACCOUNT_TRANSFER", // 流水类型名称
        "flowName": "Transfer", // 流水类型说明
        "change": "-12.5", // 变动值
        "total": "379.624059937852365", // 变动后当前tokenId总资产
        "created": "1579093587214"
    },
    {
        "id": "536072393645448960",
        "accountId": "122216245228131",
        "tokenId": "USDT",
        "tokenName": "USDT",
        "flowTypeValue": 7,
        "flowType": "AIRDROP",
        "flowName": "Airdrop",
        "change": "-2000",
        "total": "918662.0917630848",
        "created": "1578640809195"
    }
]
```

### 提现

```shell
POST /openapi/v1/withdraw
```

提现

**权重:**
1

**参数:**

| 名称               | 类型     | 是否强制 | 描述                                                   |
|------------------|--------|------|------------------------------------------------------|
| tokenId          | string | 必填   | tokenId                                              |  |
| clientOrderId    | long   | 必填   | 券商端生成的订单id， 防止重复提币                                   |  |
| address          | string | 必填   | 提币地址(注意：提现地址必须是在PC端或者APP端维护在常用地址列表里面的地址)             |  |
| addressExt       | string | 选填   | EOS tag                                              |  |
| withdrawQuantity | string | 必填   | 提币数量                                                 |  |
| chainType        | string | 非必填  | chain type, USDT的chainType分别是OMNI ERC20 TRC20，默认OMNI |

**响应:**

```json
    {
        "status": 0,
        "success": true,
        "needBrokerAudit": false, // 是否需要券商审核
        "orderId": "423885103582776064" // 提币成功订单id
    }
```

**说明**

1、主账户Api可以查询钱包账户或者其他账户(包括子账户，指定accountType和accountIndex)的流水’

2、子账户Api只能查询当前子账户的流水，所以不用指定accountType和accountIndex

**流水类型说明请见如下**

| 归类          | 类型参数名                     | 类型参数代号 | 解释说明                                    |
|-------------|---------------------------|--------|-----------------------------------------|
| 通用流水类       | TRADE                     | 1      | 交易                                      |
| 通用流水类       | FEE                       | 2      | 交易手续费                                   |
| 通用流水类       | TRANSFER                  | 3      | 转账                                      |
| 通用流水类       | DEPOSIT                   | 4      | 充值                                      |
| 衍生品业务       | MAKER_REWARD              | 27     | maker奖励                                 |
| 衍生品业务       | PNL                       | 28     | 期货等的盈亏                                  |
| 衍生品业务       | SETTLEMENT                | 30     | 交割                                      |
| 衍生品业务       | LIQUIDATION               | 31     | 强平                                      |
| 衍生品业务       | FUNDING_SETTLEMENT        | 32     | 期货等的资金费率结算                              |
| 用户子账户之间内部转账 | USER_ACCOUNT_TRANSFER     | 51     | userAccountTransfer 专用，流水没有subjectExtId |
| OTC         | OTC_BUY_COIN              | 65     | OTC 买入coin                              |
| OTC         | OTC_SELL_COIN             | 66     | OTC 卖出coin                              |
| OTC         | OTC_FEE                   | 73     | OTC 手续费                                 |
| OTC         | OTC_TRADE                 | 200    | 旧版 OTC 流水                               |
| 活动          | ACTIVITY_AWARD            | 67     | 活动奖励                                    |
| 活动          | INVITATION_REFERRAL_BONUS | 68     | 邀请返佣                                    |
| 活动          | REGISTER_BONUS            | 69     | 注册送礼                                    |
| 活动          | AIRDROP                   | 70     | 空投                                      |
| 活动          | MINE_REWARD               | 71     | 挖矿奖励                                    |
