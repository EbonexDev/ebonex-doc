# 通用信息

### 通用API信息

* 所有的端点都会返回一个JSON object或者array.
* 数据返回的是一个 **升序**。更早的在前，更新的在后。
* 所有的时间/时间戳有关的变量都是milliseconds（毫秒级）。
* HTTP `4XX` 返回错误码是指请求内容有误，这个问题是在请求发起者这边。
* HTTP `429` 返回错误码是指请求次数上限被打破。
* HTTP `418` 返回错误码是指IP在收到 `429` 错误码后还继续发送请求被自动封禁。
* HTTP `5XX` 返回错误码是内部系统错误；这说明这个问题是在券商这边。在对待这个错误时，**千万** 不要把它当成一个失败的任务，因为执行状态 **未知**，有可能是成功也有可能是失败。
* 任何端点都可能返回ERROR（错误）； 错误的返回payload如下

```json
{
  "code": 1121,
  "msg": "Invalid symbol."
}
```

* 详细的错误码和错误信息在请见错误码文件。
* 对于 `GET` 请求，必须发送参数为 `query string`（查询字串）。
* 对于 `POST`, `PUT`, 和 `DELETE` 请求，必需要发送参数为 `query string`（查询字串）或者发送参数在 `request body`（请求主体）并设置content type（内容类型）为 `application/x-www-form-urlencoded`。可以同时在 `query string` 或者 `request body` 里混合发送参数如果有需要的话。
* 参数可以以任意顺序发送。
* 如果有参数同时在 `query string` 和 `request body` 里存在，只有 `query string` 的参数会被使用。

### 限制

* 在 `/openapi/v1/brokerInfo` 的 `rateLimits` array里存在当前broker的 `REQUEST_WEIGHT` 和 `ORDER` 频率限制。
* 如果任一频率限额被超过，`429` 会被返回。
* 每条线路有一个 `weight` (**权重**)特性，这个决定了这个请求占用多少容量（比如 `weight` =2说明这个请求占用两个请求的量,即请求一次，系统限流计数加2）。返回数据多的端点或者在多个symbol执行任务的端点可能有更高的 `weight`。
* 当 `429` 被返回后，你有义务停止发送请求。
* **多次反复违反频率限制和/或者没有在收到429后停止发送请求的用户将会被收到封禁IP（错误码418）**
* IP封禁会被跟踪和 **调整封禁时长**（对于反复违反规定的用户，时间从 **2分钟到3天不等**）

### 端点安全类型

* 每个端点有一个安全类型，这决定了你会怎么跟其交互。
* API-key要以 `X-EBONEX-APIKEY` 的名字传到REST API header里面。
* API-keys和secret-keys **要区分大小写**。
* 默认情况下，API-keys可以访问所有的安全节点。

| 安全类型        | 描述                   |
|-------------|----------------------|
| NONE        | 端点可以自由访问。            |
| TRADE       | 端点需要发送有效的API-Key和签名。 |
| USER_DATA   | 端点需要发送有效的API-Key和签名。 |
| USER_STREAM | 端点需要发送有效的API-Key。    |
| MARKET_DATA | 端点需要发送有效的API-Key。    |

* `TRADE` 和 `USER_DATA` 端点是 `SIGNED`（需要签名）的端点。

### SIGNED（有签名的）(TRADE和USER_DATA) 端点安全

* `SIGNED`（需要签名）的端点需要发送一个参数，`signature`，在`query string` 或者 `request body`里。
* 端点用 `HMAC SHA256` 签名。`HMAC SHA256 signature` 是一个对key进行 `HMAC SHA256` 加密的结果。用你的 `secretKey` 作为key和 `totalParams` 作为value来完成这一加密过程。
* `signature`  **不区分大小写**。
* `totalParams` 是指 `query string` 串联 `request body`。

### 时效安全

* 一个 `SIGNED` (有签名)的端点还需要发送一个参数，`timestamp`，这是当请求发起时的毫秒级时间戳。
* 一个额外的参数（非强制性）, `recvWindow`, 可以说明这个请求在多少毫秒内是有效的。如果 `recvWindow` 没有被发送，**默认值是5000**。
* 在当前，只有创建订单的时候才会用到 `recvWindow`。
* 该参数的逻辑如下：

  ```javascript
  if (timestamp < (serverTime + 1000) && (serverTime - timestamp) <= recvWindow) {
    // process request
  } else {
    // reject request
  }
  ```

**严谨的交易和时效紧紧相关** 网络有时会不稳定或者不可靠，这会导致请求发送服务器的时间不一致。
有了 `recvWindow`，你可以说明在多少毫秒内请求是有效的，否则就会被服务器拒绝。

**建议使用一个相对小的recvWindow（5000或以下）！**

### SIGNED（签名） 的例子（对于POST /openapi/v1/order）

这里有一个详细的用Linux `echo`, `openssl`, 和 `curl` 举例来展示如何发送一个有效的签名payload。

| Key       | 值                                                                |
|-----------|------------------------------------------------------------------|
| apiKey    | tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW |
| secretKey | lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76 |

| 参数名         | 参数值           |
|-------------|---------------|
| symbol      | ETHBTC        |
| side        | BUY           |
| type        | LIMIT         |
| timeInForce | GTC           |
| quantity    | 1             |
| price       | 0.1           |
| recvWindow  | 5000          |
| timestamp   | 1538323200000 |

#### 例子 1: 在 `queryString` 里

* **queryString:** symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000
* **HMAC SHA256 signature:**

```shell
[linux]$ echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"
(stdin)= 5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6
```

* **curl command:**

```shell
(HMAC SHA256)
[linux]$ curl -H "X-EBONEX-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/openapi/v1/order?symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6'
```

#### 例子 2:  在 `request body` 里

* **requestBody:** symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000
* **HMAC SHA256 signature:**

```shell
[linux]$ echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"
(stdin)= 5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6
```

* **curl command:**

```shell
(HMAC SHA256)
[linux]$ curl -H "X-EBONEX-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/openapi/v1/order' -d 'symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC&quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=5f2750ad7589d1d40757a55342e621a44037dad23b5128cc70e18ec1d1c3f4c6'
```

#### 例子 3: `queryString` 和 `request body` 混合在一起

* **queryString:** symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC
* **requestBody:** quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000
* **HMAC SHA256 signature:**

```shell
[linux]$ echo -n "symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTCquantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000" | openssl dgst -sha256 -hmac "lH3ELTNiFxCQTmi9pPcWWikhsjO04Yoqw3euoHUuOLC3GYBW64ZqzQsiOEHXQS76"
(stdin)= 885c9e3dd89ccd13408b25e6d54c2330703759d7494bea6dd5a3d1fd16ba3afa
```

* **curl command:**

```shell
(HMAC SHA256)
[linux]$ curl -H "X-EBONEX-APIKEY: tAQfOrPIZAhym0qHISRt8EFvxPemdBm5j5WMlkm3Ke9aFp0EGWC2CGM8GHV4kCYW" -X POST 'https://$HOST/openapi/v1/order?symbol=ETHBTC&side=BUY&type=LIMIT&timeInForce=GTC' -d 'quantity=1&price=0.1&recvWindow=5000&timestamp=1538323200000&signature=885c9e3dd89ccd13408b25e6d54c2330703759d7494bea6dd5a3d1fd16ba3afa'
```

***注意在例子3里有一点不一样，"GTC"和"quantity=1"之间没有&。***

### Open API 参数

#### 术语解释

* `base asset` 指的是symbol的 `quantity`（即数量）。

* `quote asset` 指的是symbol的 `price`（即价格）。

#### 枚举定义

**交易对状态:**

* TRADING - 交易中
* HALT - 终止
* BREAK - 断开

**交易对类型:**

* SPOT - 现货

**资产类型:**

* CASH - 现金
* MARGIN - 保证金

**订单状态:**

* NEW - 新订单，暂无成交
* PARTIALLY_FILLED - 部分成交
* FILLED - 完全成交
* CANCELED - 已取消(部分成交再取消订单状态也会变成已取消)
* PENDING_CANCEL - 等待取消
* REJECTED - 被拒绝

**订单类型(orderType,type):**

* LIMIT - 限价单
* MARKET - 市价单
* LIMIT_MAKER - maker限价单
* STOP_LOSS (unavailable now)  - 暂无
* STOP_LOSS_LIMIT (unavailable now) - 暂无
* TAKE_PROFIT (unavailable now) - 暂无
* TAKE_PROFIT_LIMIT (unavailable now) - 暂无
* MARKET_OF_PAYOUT (unavailable now) - 暂无

**订单方向(orderSide,side):**

* BUY - 买单
* SELL - 卖单

**订单时效类型:**

* GTC
* IOC
* FOK

**k线间隔:**

m -> 分钟; h -> 小时; d -> 天; w -> 周; M -> 月

* 1m
* 3m
* 5m
* 15m
* 30m
* 1h
* 2h
* 4h
* 6h
* 8h
* 12h
* 1d
* 3d
* 1w
* 1M

**频率限制类型 (rateLimitType)**

* REQUESTS_WEIGHT
* ORDERS

**频率限制区间**

* SECOND
* MINUTE
* DAY

### 过滤器

过滤层（filter）定义symbol的交易规则

#### Symbol过滤层

##### PRICE_FILTER

`PRICE_FILTER` 定义某个symbol的 `price` 精度. 一共有3个部分：

* `minPrice` 定义最小允许的 `price`/`stopPrice`
* `maxPrice` 定义最大允许的 `price`/`stopPrice`.
* `tickSize` 定义 `price`/`stopPrice` 可以增加和减少的间隔。

如果要通过 `price filter` 要求，`price`/`stopPrice` 必须满足：

* `price` >= `minPrice`
* `price` <= `maxPrice`
* (`price`-`minPrice`) % `tickSize` == 0

**filter 格式:**

```json
  {
    "filterType": "PRICE_FILTER",
    "minPrice": "0.00000100",
    "maxPrice": "100000.00000000",
    "tickSize": "0.00000100"
  }
```

##### LOT_SIZE

`LOT_SIZE` 过滤层定义某个symbol `quantity` (在拍卖行里又称为"lots"）的精度。 一共有三个部分：

* `minQty` 定义最小允许的  `quantity`/`icebergQty`
* `maxQty` 定义最大允许的  `quantity`/`icebergQty`
* `stepSize` 定义 `quantity`/`icebergQty` 可以增加和减少的间隔。

如果要通过 `lot size` 要求，`quantity`/`icebergQty` 必须满足:

* `quantity` >= `minQty`
* `quantity` <= `maxQty`
* (`quantity`-`minQty`) % `stepSize` == 0

**filter 格式:**

```json
  {
    "filterType": "LOT_SIZE",
    "minQty": "0.00100000",
    "maxQty": "100000.00000000",
    "stepSize": "0.00100000"
  }
```

##### MIN_NOTIONAL

`MIN_NOTIONAL` 过滤层定义某个symbol的名义金额精度。一个订单的名义金额为 `price` * `quantity`.

**filter 格式:**

```json
  {
    "filterType": "MIN_NOTIONAL",
    "minNotional": "0.00100000"
  }
```
