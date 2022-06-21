## 通用接口 

### 测试连接

```shell
GET /openapi/v1/ping
```

测试REST API的连接。

**权重:** 0

**参数:** NONE

**响应::**

```json
{}
```

### 服务器时间

```shell
GET /openapi/v1/time
```

测试连接并获取当前服务器的时间。

**权重:** 0

**参数:** NONE

**响应::**

```json
{
  "serverTime": 1538323200000
}
```

### 交易规范信息

```shell
GET /openapi/v1/exchangeInfo
```

当前交易规则和symbol信息

**权重:** 0

**参数:** NONE

**响应::**

```json
{
  "timezone": "UTC",
  "serverTime": 1538323200000,
  "rateLimits": [{
      "rateLimitType": "REQUESTS_WEIGHT",
      "interval": "MINUTE",
      "limit": 1500
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "SECOND",
      "limit": 20
    },
    {
      "rateLimitType": "ORDERS",
      "interval": "DAY",
      "limit": 350000
    }
  ],
  "symbols": [{
    "symbol": "ETHBTC",
    "status": "TRADING",
    "baseAsset": "ETH",
    "baseAssetPrecision": "0.001",
    "quoteAsset": "BTC",
    "quotePrecision": "0.01",
    "icebergAllowed": false,
    "filters": [{
      "filterType": "PRICE_FILTER",
      "minPrice": "0.00000100",
      "maxPrice": "100000.00000000",
      "tickSize": "0.00000100"
    }, {
      "filterType": "LOT_SIZE",
      "minQty": "0.00100000",
      "maxQty": "100000.00000000",
      "stepSize": "0.00100000"
    }, {
      "filterType": "MIN_NOTIONAL",
      "minNotional": "0.00100000"
    }]
  }]
}
```