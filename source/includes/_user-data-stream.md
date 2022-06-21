## 用户数据流端点

### WSS 通用信息

* 一个用户流的 `listenKey` 过期时间为60分钟.
* 对一个 `listenKey` 做一个 `PUT` 请求将延长其有效期60分钟。
* 在 `listenKey` 上的 `DELETE` 请求会关闭用户流.
* 用户流连接地址： `/openapi/ws/<listenKey>`
* 与api端点的单一连接只在24小时内有效；预计在24小时后会被断开连接。
* 用户数据流的有效载荷在繁重时期不能 **保证** 其顺序

### 开始用户信息流 (USER_STREAM)

```shell
POST /openapi/v1/userDataStream
```

开始一个新的用户信息流。如果 `keepalive` 指令没有发送，信息流将将会在60分钟后关闭。

**权重:** 1

**参数:**

| 名称         | 类型   | 是否强制 | 描述  |
|------------|------|------|-----|
| recvWindow | LONG | NO   |     |
| timestamp  | LONG | YES  |     |

**响应:**

```json
{
  "listenKey": "1A9LWJjuMwKWYP4QQPw34GRm8gz3x5AephXSuqcDef1RnzoBVhEeGE963CoS1Sgj"
}
```

### Keepalive用户信息流 (USER_STREAM)

```shell
PUT /openapi/v1/userDataStream
```

维持用户信息流来防止断开连接。用户信息流会在60分钟后自动中断，所以建议30分钟发送一次ping请求。

**权重:** 1

**参数:**

| 名称         | 类型     | 是否强制 | 描述  |
|------------|--------|------|-----|
| listenKey  | STRING | YES  |     |
| recvWindow | LONG   | NO   |     |
| timestamp  | LONG   | YES  |     |

**响应:**

```json
{}
```

### 关闭用户信息流 (USER_STREAM)

```shell
DELETE /openapi/v1/userDataStream
```

关闭用户信息流

**权重:** 1

**参数:**

| 名称         | 类型     | 是否强制 | 描述  |
|------------|--------|------|-----|
| listenKey  | STRING | YES  |     |
| recvWindow | LONG   | NO   |     |
| timestamp  | LONG   | YES  |     |

**响应:**

```json
{}
```

### Websocket Payloads

#### 账号修改通知

账号状态修改伴随 `outboundAccountInfo` 事件通知.

**响应:**

```json
[
  {
    "e": "outboundAccountInfo", // 事件类型
    "E": "1655255177260", // 事件类型
    "T": true, // 能够交易
    "W": true, // 是否可提取
    "D": true, // 是否可充值
    "B": [ // 余额变动
      {
        "a": "USDT", // 资产
        "f": "1061438.2", // 可用金额
        "l": "300" // 锁定金额
      }
    ]
  }
]
```

#### 订单修改通知

订单修改伴随 `executionReport` 事件通知. 查看[通用文档](./generic.md)和下面的相关枚举定义。平均价格可以通过 `Z` / `z` 计算得到

**响应:**

```json
[
  {
    "e": "executionReport", // 事件类型
    "E": "1655255177254", // 事件时间
    "s": "BTCUSDT", // 交易对
    "c": "165525517720480", // Client order ID
    "S": "BUY", // 订单方向
    "o": "LIMIT", // 订单类型
    "f": "GTC", // Time in force
    "q": "0.01", // 订单数量
    "p": "30000", // 订单价格
    "X": "NEW", // 订单当前状态
    "i": "1178760294099355648", // 订单ID
    "M": "0", // 撮合订单ID
    "l": "0", // Last executed quantity
    "z": "0", // Cumulative filled quantity
    "L": "0", // Last executed price
    "n": "0", // Commission amount
    "N": "", // Commission asset
    "u": true, // Is the trade normal, ignore for now
    "w": true, // Is the order working? Stops will have
    "m": false, // Is this trade the maker side?
    "O": "1655255177214", // 订单创建时间
    "Z": "0", // Cumulative quote asset transacted quantity
    "A": "0", // 撮合账户ID
    "C": false, // 订单是否关闭
    "v": "0" // 杠杆倍数
  }
]
```

**订单状态:**

详情可见[通用说明枚举定义](./generic.md)

* NEW
* PARTIALLY_FILLED
* FILLED
* CANCELED
* REJECTED
