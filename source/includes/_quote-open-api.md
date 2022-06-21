# 行情接口

### 深度信息

```shell
GET /openapi/quote/v1/depth
```

**权重:**

根据limit不同：

| Limit 参数           | 权重  |
|--------------------|-----|
| 5, 10, 20, 50, 100 | 1   |
| 500                | 5   |
| 1000               | 10  |

**参数:**

| 名称     | 类型     | 是否强制 | 描述              |
|--------|--------|------|-----------------|
| symbol | STRING | YES  |                 |
| limit  | INT    | NO   | 默认 100; 最大 100. |

**注意:** 如果设置limit=0会返回很多数据。

**响应:**

[价格, 数量]

```json
{
  "bids": [
    [
      "3.90000000", // 价格
      "431.00000000"// 数量
    ],
    [
      "4.00000000",
      "431.00000000"
    ]
  ],
  "asks": [
    [
      "4.00000200", // 价格
      "12.00000000" // 数量
    ],
    [
      "5.10000000",
      "28.00000000"
    ]
  ]
}
```

### 最近成交

```shell
GET /openapi/quote/v1/trades
```

获取当前最新成交（最多60）

**权重:** 1

**参数:**

| 名称     | 类型     | 是否强制 | 描述                  |
|--------|--------|------|---------------------|
| symbol | STRING | YES  |                     |
| limit  | INT    | NO   | Default 60; max 60. |

**响应:**

```json
[
  {
    "price": "4.00000100",
    "qty": "12.00000000",
    "time": 1499865549590,
    "isBuyerMaker": true
  }
]
```

### k线/烛线图数据

```shell
GET /openapi/quote/v1/klines
```

symbol的k线/烛线图数据
K线会根据开盘时间而辨别。

**权重:** 1

**参数:**

| 名称        | 类型     | 是否强制 | 描述                          |
|-----------|--------|------|-----------------------------|
| symbol    | STRING | YES  |                             |
| interval  | ENUM   | YES  | 详见枚举定义：[K线间隔](./generic.md) |
| startTime | LONG   | NO   |                             |
| endTime   | LONG   | NO   |                             |
| limit     | INT    | NO   | 默认500; 最大1000.              |

* 如果startTime和endTime没有发送，只有最新的K线会被返回。

**响应:**

```javascript
[
    [
        1499040000000,      // 开盘时间
        "0.01634790",       // 开盘价
        "0.80000000",       // 最高价
        "0.01575800",       // 最低价
        "0.01577100",       // 收盘价
        "148976.11427815",  // 交易量
        1499644799999,      // 收盘时间
        "2434.19055334",    // Quote asset数量
        308,                // 交易次数
        "1756.87402397",    // Taker buy base asset数量
        "28.46694368"       // Taker buy quote asset数量
    ]
]
```

### 24小时ticker价格变化数据

```shell
GET /openapi/quote/v1/ticker/24hr
```

24小时价格变化数据。**注意** 如果没有发送symbol，会返回很多数据。

**权重:**

如果只有一个symbol，1; 如果symbol没有被发送，**40**。

**参数:**

| 名称     | 类型     | 是否强制 | 描述  |
|--------|--------|------|-----|
| symbol | STRING | NO   |     |

* 如果symbol没有被发送，所有symbol的数据都会被返回。

**响应:**

```json
{
  "time": 1538725500422,
  "symbol": "ETHBTC",
  "bestBidPrice": "4.00000200",
  "bestAskPrice": "4.00000200",
  "lastPrice": "4.00000200",
  "openPrice": "99.00000000",
  "highPrice": "100.00000000",
  "lowPrice": "0.10000000",
  "volume": "8913.30000000"
}
```

OR

```json
[
  {
    "time": 1538725500422,
    "symbol": "ETHBTC",
    "lastPrice": "4.00000200",
    "openPrice": "99.00000000",
    "highPrice": "100.00000000",
    "lowPrice": "0.10000000",
    "volume": "8913.30000000"
  }
]
```

### 交易对价格

```shell
GET /openapi/quote/v1/ticker/price
```

单个或多个symbol的最新价。

**权重:** 1

**参数:**

| 名称     | 类型     | 是否强制 | 描述  |
|--------|--------|------|-----|
| symbol | STRING | NO   |     |

* 如果symbol没有发送，所有symbol的最新价都会被返回。

**响应:**

```json
{
  "price": "4.00000200"
}
```

OR

```json
[
  {
    "symbol": "LTCBTC",
    "price": "4.00000200"
  },
  {
    "symbol": "ETHBTC",
    "price": "0.07946600"
  }
]
```

### 交易对最佳订单簿价格

```shell
GET /openapi/quote/v1/ticker/bookTicker
```

单个或者多个symbol的最佳买单卖单价格。

**权重:** 1

**参数:**

| 名称     | 类型     | 是否强制 | 描述  |
|--------|--------|------|-----|
| symbol | STRING | NO   |     |

* 如果symbol没有被发送，所有symbol的最佳订单簿价格都会被返回。

**响应:**

```json
{
  "symbol": "LTCBTC",
  "bidPrice": "4.00000000",
  "bidQty": "431.00000000",
  "askPrice": "4.00000200",
  "askQty": "9.00000000"
}
```

OR

```json
[
  {
    "symbol": "LTCBTC",
    "bidPrice": "4.00000000",
    "bidQty": "431.00000000",
    "askPrice": "4.00000200",
    "askQty": "9.00000000"
  },
  {
    "symbol": "ETHBTC",
    "bidPrice": "0.07946700",
    "bidQty": "9.00000000",
    "askPrice": "100000.00000000",
    "askQty": "1000.00000000"
  }
]
```