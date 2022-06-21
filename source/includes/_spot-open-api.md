# 现货账户和交易接口

### 现货币对

```shell
GET /openapi/v1/symbol
```

列出所有现货的币对列表，包括baseToken和quoteToken

**权重:** 1

**参数:**

无

**响应:**

```json
[
  {
    "symbol": "BTCUSDT",
    "quoteToken": "USDT",
    "baseToken": "BTC"
  },
  {
    "symbol": "EBTEUSD",
    "quoteToken": "EUSD",
    "baseToken": "EBT"
  },
  {
    "symbol": "DEADEAUSDT",
    "quoteToken": "USDT",
    "baseToken": "DEADEA"
  },
  {
    "symbol": "SB001USDT",
    "quoteToken": "USDT",
    "baseToken": "SB001"
  },
  {
    "symbol": "SB006USDT",
    "quoteToken": "USDT",
    "baseToken": "SB006"
  }
]
```

### 测试下单 (TRADE)

```shell
POST /openapi/v1/order/test (HMAC SHA256)
```

用signature和recvWindow测试生成新订单。
创建和验证一个新订单但是不送入撮合引擎。

**权重:** 1

**参数:**

和 `POST /openapi/v1/order` 一样。

**响应:**

```json
{}
```

### 下单  (TRADE)

```shell
POST /openapi/v1/order  (HMAC SHA256)
```

发送一个新的订单

**权重:** 1

**参数:**

| 名称               | 类型      | 是否强制 | 描述                                                                               |
|------------------|---------|------|----------------------------------------------------------------------------------|
| symbol           | STRING  | YES  |                                                                                  |
| assetType        | STRING  | NO   |                                                                                  |
| side             | ENUM    | YES  | 详见枚举定义：[订单方向](./generic.md)                                                      |
| type             | ENUM    | YES  | 详见枚举定义：[订单类型](./generic.md)                                                      |
| timeInForce      | ENUM    | NO   | 详见枚举定义：[订单时效类型](./generic.md)                                                    |
| quantity         | DECIMAL | YES  |                                                                                  |
| price            | DECIMAL | NO   |                                                                                  |
| newClientOrderId | STRING  | NO   | 客户自定义的订单ID，如果没有发送会自动生成。                                                          |
| stopPrice        | DECIMAL | NO   | 与 `STOP_LOSS`, `STOP_LOSS_LIMIT`, `TAKE_PROFIT`, 和`TAKE_PROFIT_LIMIT` 订单一起使用. ** |
| 当前不可用**          |         |      |                                                                                  |
| icebergQty       | DECIMAL | NO   | 与 `LIMIT`, `STOP_LOSS_LIMIT`, 和 `TAKE_PROFIT_LIMIT` 来创建冰山订单. **                  |
| 当前不可用**          |         |      |                                                                                  |
| recvWindow       | LONG    | NO   |                                                                                  |
| timestamp        | LONG    | YES  |                                                                                  |

在 `type` 上的额外强制参数:

| 类型                  | 额外强制参数                                                     |
|---------------------|------------------------------------------------------------|
| `LIMIT`             | `timeInForce`, `quantity`, `price`                         |
| `MARKET`            | `quantity`                                                 |
| `STOP_LOSS`         | `quantity`, `stopPrice`  **当前不可用**                         |
| `STOP_LOSS_LIMIT`   | `timeInForce`, `quantity`,  `price`, `stopPrice` **当前不可用** |
| `TAKE_PROFIT`       | `quantity`, `stopPrice` **当前不可用**                          |
| `TAKE_PROFIT_LIMIT` | `timeInForce`, `quantity`, `price`, `stopPrice` **当前不可用**  |
| `LIMIT_MAKER`       | `quantity`, `price`                                        |

**响应:**

```json
{
  "accountId ": "1110118919514983321 ",
  "symbol ": "BTCUSDT ",
  "symbolName ": "BTCUSDT ",
  "clientOrderId ": "165511953378788 ",
  "orderId ": "1177622434654849536 ",
  "transactTime ": "1655119533798 ",
  "price ": "30000 ",
  "origQty ": "0.01 ",
  "executedQty ": "0 ",
  "status ": "NEW ",
  "timeInForce ": "GTC ",
  "type ": "LIMIT ",
  "side ": "SELL "
}
```

### 批量下单

```shell
POST /openapi/v1/batch-order  (HMAC SHA256)
```

同时下多个订单，不能超过50个

**权重:** 1

**参数:**
参数是下单接口参数组成的jsonArray

**响应:**

```json
[
  {
    "orderid": "1178087746521103872",
    "clientorderid": "165517500326464"
  },
  {
    "orderid": "1178087747502571008",
    "clientorderid": "165517500338664"
  }
]
```

### 查询订单 (USER_DATA)

```shell
GET /openapi/v1/order (HMAC SHA256)
```

查询订单状态。

**权重:** 1

**参数:**

| 名称                | 类型     | 是否强制 | 描述  |
|-------------------|--------|------|-----|
| orderId           | LONG   | NO   |     |
| origClientOrderId | STRING | NO   |     |
| recvWindow        | LONG   | NO   |     |
| timestamp         | LONG   | YES  |     |

注意:

* 单一 `orderId` 或者 `origClientOrderId` 必须被发送。
* 对于某些历史数据 `cummulativeQuoteQty` 可能会 < 0, 这说明数据当前不可用。

**响应:**

```json
{
  "accountId": "1110118919514983321",
  "exchangeId": "301",
  "symbol": "BTCUSDT",
  "symbolName": "BTCUSDT",
  "clientOrderId": "165511953378788",
  "orderId": "1177622434654849536",
  "price": "30000",
  "origQty": "0.01",
  "executedQty": "0.01",
  "cummulativeQuoteQty": "300",
  "avgPrice": "30000",
  "status": "FILLED",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "SELL",
  "stopPrice": "0.0",
  "icebergQty": "0.0",
  "time": "1655119527291",
  "updateTime": "1655119527291",
  "isWorking": true
}
```

### 取消订单 (TRADE)

```shell
DELETE /openapi/v1/order  (HMAC SHA256)
```

取消当前正在交易的订单。

**权重:** 1

**参数:**

| 名称            | 类型     | 是否强制 | 描述  |
|---------------|--------|------|-----|
| orderId       | LONG   | NO   |     |
| clientOrderId | STRING | NO   |     |
| recvWindow    | LONG   | NO   |     |
| timestamp     | LONG   | YES  |     |

单一 `orderId` 或者 `clientOrderId` 必须被发送。

**响应:**

```json
{
  "accountId": "1110118919514983321",
  "symbol": "BTCUSDT",
  "clientOrderId": "165517620695768",
  "orderId": "1178097843662061056",
  "transactTime": "1655176200144",
  "price": "30000",
  "origQty": "0.01",
  "executedQty": "0",
  "status": "NEW",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "BUY"
}
```

### 批量取消订单

```shell
GET /openapi/v1/order/batchCancel   (HMAC SHA256)
```

可以取消单个交易对下的买单、卖单、所有订单或者账户下所有的现货订单，批量撤单最大限制 500 个

**权重:** 5

**参数:**

| 名称         | 类型     | 是否强制 | 描述   |
|------------|--------|------|------|
| symbol     | STRING | NO   | 交易对  |
| side       | STRING | NO   | 订单方向 |
| recvWindow | LONG   | NO   |      |
| timestamp  | LONG   | YES  |      |

**响应:**

```json
{
  "success": true
}
```

### 当前订单(USER_DATA)

```shell
GET /openapi/v1/openOrders  (HMAC SHA256)
```

获取当前单个或者多个symbol的当前订单。**注意** 如果没有发送symbol，会返回很多数据。

**权重:** 1

**参数:**

| 名称         | 类型     | 是否强制 | 描述               |
|------------|--------|------|------------------|
| symbol     | String | NO   |                  |
| orderId    | LONG   | NO   |                  |
| limit      | INT    | NO   | 默认 500; 最多 1000. |
| recvWindow | LONG   | NO   |                  |
| timestamp  | LONG   | YES  |                  |

**注意:**

* 如果传入参数 `orderId` ，返回小于 `orderId` 的订单。否则会返回最近的订单信息。

**响应:**

```json
[
  {
    "accountId": "1110118919514983321",
    "exchangeId": "301",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "clientOrderId": "165517651725386",
    "orderId": "1178100446630346240",
    "price": "30000",
    "origQty": "0.01",
    "executedQty": "0",
    "cummulativeQuoteQty": "0",
    "avgPrice": "0",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": "1655176510440",
    "updateTime": "1655176517264",
    "isWorking": true
  },
  {
    "accountId": "1110118919514983321",
    "exchangeId": "301",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "clientOrderId": "165517651710186",
    "orderId": "1178100445397220864",
    "price": "30000",
    "origQty": "0.01",
    "executedQty": "0",
    "cummulativeQuoteQty": "0",
    "avgPrice": "0",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": "1655176510372",
    "updateTime": "1655176517116",
    "isWorking": true
  },
  {
    "accountId": "1110118919514983321",
    "exchangeId": "301",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "clientOrderId": "165517651691486",
    "orderId": "1178100443803385344",
    "price": "30000",
    "origQty": "0.01",
    "executedQty": "0",
    "cummulativeQuoteQty": "0",
    "avgPrice": "0",
    "status": "NEW",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "BUY",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": "1655176510114",
    "updateTime": "1655176516930",
    "isWorking": true
  }
]
```

### 历史订单 (USER_DATA)

```shell
GET /openapi/v1/historyOrders (HMAC SHA256)
```

获取当前账户的所有订单。亦或是取消的，完全成交的，拒绝的。

**权重:** 5

**参数:**

| 名称         | 类型     | 是否强制 | 描述                     |
|------------|--------|------|------------------------|
| symbol     | String | NO   |                        |
| orderId    | LONG   | NO   |                        |
| startTime  | LONG   | NO   |                        |
| endTime    | LONG   | NO   |                        |
| limit      | INT    | NO   | Default 500; max 1000. |
| recvWindow | LONG   | NO   |                        |
| timestamp  | LONG   | YES  |                        |

**注意:**

* 如果传入参数 `orderId` ，返回小于 `orderId` 的订单。否则会返回最近的订单信息。

**响应:**

```json
[
  {
    "accountId": "1110118919514983321",
    "exchangeId": "301",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "clientOrderId": "165511953378788",
    "orderId": "1177622434654849536",
    "price": "30000",
    "origQty": "0.01",
    "executedQty": "0.01",
    "cummulativeQuoteQty": "300",
    "avgPrice": "30000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": "1655119527291",
    "updateTime": "1655119527291",
    "isWorking": true
  },
  {
    "accountId": "1110118919514983321",
    "exchangeId": "301",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "clientOrderId": "1655119411992",
    "orderId": "1177621417074756096",
    "price": "30000",
    "origQty": "0.01",
    "executedQty": "0.01",
    "cummulativeQuoteQty": "300",
    "avgPrice": "30000",
    "status": "FILLED",
    "timeInForce": "GTC",
    "type": "LIMIT",
    "side": "SELL",
    "stopPrice": "0.0",
    "icebergQty": "0.0",
    "time": "1655119405760",
    "updateTime": "1655119405761",
    "isWorking": true
  }
]
```

### 账户信息 (USER_DATA)

```shell
GET /openapi/v1/account (HMAC SHA256)
```

获取当前账户信息

**权重:** 5

**参数:**

| 名称         | 类型   | 是否强制 | 描述  |
|------------|------|------|-----|
| recvWindow | LONG | NO   |     |
| timestamp  | LONG | YES  |     |

**响应:**

```json
{
  "balances": [
    {
      "asset": "BTC",
      "assetId": "BTC",
      "assetName": "BTC",
      "total": "0.05",
      "free": "0.05",
      "locked": "0"
    },
    {
      "asset": "EBT",
      "assetId": "EBT",
      "assetName": "EBT",
      "total": "10000000000",
      "free": "10000000000",
      "locked": "0"
    },
    {
      "asset": "EUSD",
      "assetId": "EUSD",
      "assetName": "EUSD",
      "total": "100000000000000000",
      "free": "100000000000000000",
      "locked": "0"
    },
    {
      "asset": "USDT",
      "assetId": "USDT",
      "assetName": "USDT",
      "total": "599.4",
      "free": "599.4",
      "locked": "0"
    }
  ]
}

```

### 账户交易记录 (USER_DATA)

```shell
GET /openapi/v1/myTrades  (HMAC SHA256)
```

获取当前账户历史成交记录

**权重:** 5

**参数:**

| 名称         | 类型   | 是否强制 | 描述                     |
|------------|------|------|------------------------|
| startTime  | LONG | NO   |                        |
| endTime    | LONG | NO   |                        |
| fromId     | LONG | NO   | TradeId to fetch from. |
| toId       | LONG | NO   | TradeId to fetch to.   |
| limit      | INT  | NO   | Default 500; max 1000. |
| recvWindow | LONG | NO   |                        |
| timestamp  | LONG | YES  |                        |

**注意:**

* 如果只有 `fromId` ，会返回订单号小于 `fromId` 的，倒序排列。
* 如果只有 `toId` ，会返回订单号小于 `toId` 的，升序排列。
* 如果同时有 `fromId` 和 `toId` , 会返回订单号在 `fromId` 和 `toId` 之间的，倒序排列。
* 如果 `fromId` 和 `toId` 都没有，会返回最新的成交记录，倒序排列。

**响应:**

```json
[
  {
    "id": "1177622437364369921",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "orderId": "1177622434654849536",
    "matchOrderId": "1168769052162760960",
    "price": "30000",
    "qty": "0.01",
    "commission": "0.3",
    "commissionAsset": "USDT",
    "time": "1655119534081",
    "isBuyer": false,
    "isMaker": false,
    "fee": {
      "feeTokenId": "USDT",
      "feeTokenName": "USDT",
      "fee": "0.3"
    },
    "feeTokenId": "USDT",
    "feeAmount": "0.3",
    "makerRebate": "0"
  },
  {
    "id": "1177621417846508033",
    "symbol": "BTCUSDT",
    "symbolName": "BTCUSDT",
    "orderId": "1177621417074756096",
    "matchOrderId": "1168769052162760960",
    "price": "30000",
    "qty": "0.01",
    "commission": "0.3",
    "commissionAsset": "USDT",
    "time": "1655119412506",
    "isBuyer": false,
    "isMaker": false,
    "fee": {
      "feeTokenId": "USDT",
      "feeTokenName": "USDT",
      "fee": "0.3"
    },
    "feeTokenId": "USDT",
    "feeAmount": "0.3",
    "makerRebate": "0"
  }
]
```

