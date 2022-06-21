# 错误码

错误由两部分组成：错误码和消息；错误码是通用的，但是消息可能会有所不同



 错误 JSON 格式:

```javascript
{
  "code":-1121,
  "msg":"Invalid symbol."
}
```

### 10xx - 常规服务器和网络问题

#### 1000 UNKNOWN

* 处理请求时发生未知错误。

#### 1001 DISCONNECTED

* 内部错误；无法处理您的请求。请再试一次。

#### 1002 UNAUTHORIZED

* 您无权执行此请求. 请求需要带上 API Key.我们建议您在任何请求中带上 API Key。

#### 1003 TOO_MANY_REQUESTS

* 请求过多; 请使用websocket 获取实时更新。
* 请求过多; 当前限制为每分钟 `%s` 请求权重。请使用 websocket 进行实时更新，以避免轮询 API。
* 请求过多; IP 已被禁止，直到 `%s` 。 请使用websocket进行实时更新，以免被禁。

#### 1006 UNEXPECTED_RESP

* 从消息总线收到意外的响应。 执行状态未知。执行状态未知。OPEN API 执行请求中发现异常，请上报给客服。

#### 1007 TIMEOUT

* 等待后端服务器响应超时。 发送状态未知； 执行状态未知。

#### 1014 UNKNOWN_ORDER_COMPOSITION

* 不支持的订单组合

#### 1015 TOO_MANY_ORDERS

* 达到请求频率限制 .请减慢您的请求频率
* 新订单太多
* 新订单太多;当前限制是每 `%s` 时间订单个数为 `%s`

#### 1016 SERVICE_SHUTTING_DOWN

* 该服务不可用

#### 1020 UNSUPPORTED_OPERATION

* 不支持此操作

#### 1021 INVALID_TIMESTAMP

* 此请求的时间戳在 recvWindow 之外
* 此请求的时间戳比服务器时间提前 1000 毫秒
* 请检查你的本地时间和服务器时间差值

#### 1022 INVALID_SIGNATURE

* 此次请求签名无效

### 11xx - 请求问题

#### 1100 ILLEGAL_CHARS

* 在参数中发现非法字符
* 在参数 `%s`中发现非法字符； 合法范围是 `%s`

#### 1101 TOO_MANY_PARAMETERS

* 为此端点发送的太多参数
* 太多参数； 预期为 `%s` 实际收到 `%s`
* 检测到的参数值重复

#### 1102 MANDATORY_PARAM_EMPTY_OR_MALFORMED

* 未发送强制性参数，该参数为空/空或格式错误
* 强制参数`%s`未发送，为空/空或格式错误。
* 必须发送参数`%s`或`%s`，但两者均为空！

#### 1103 UNKNOWN_PARAM

* 发送了未知参数
* 在 Ebonex Open API 中, 每个请求至少会有一个参数 {TimeStamp}

#### 1104 UNREAD_PARAMETERS

* 并非所有发送的参数都被读取.
* 并非所有发送的参数都被读取； 读取了`%s`参数，但被发送了`%s`。

#### 1105 PARAM_EMPTY

* 参数为空
* 参数 `%s` 为空

#### 1106 PARAM_NOT_REQUIRED

* 在不需要时发送了参数
* 参数 `%s` 在不需要时被发送

#### 1111 BAD_PRECISION

* 精度超过为此资产定义的最大值

#### 1112 NO_DEPTH

* 交易对没有挂单

#### 1114 TIF_NOT_REQUIRED

* 不需要时发送了 TimeInForce 参数

#### 1115 INVALID_TIF

* 无效的 timeInForce.
* 在当前版本, 此参数的值要么为空要么是 GTC

#### 1116 INVALID_ORDER_TYPE

* 无效的订单类型
* 在当前版本 , 订单类型的值只能是 LIMIT 或 MARKET.

#### 1117 INVALID_SIDE

* 无效的订单方向
* 订单方向的值只能是 BUY 或 SELL

#### 1118 EMPTY_NEW_CL_ORD_ID

* 新的客户订单 ID 为空

#### 1119 EMPTY_ORG_CL_ORD_ID

* 客户自定义的订单 ID 为空

#### 1120 BAD_INTERVAL

* 无效的时间间隔

#### 1121 BAD_SYMBOL

* 无效的交易对

#### 1125 INVALID_LISTEN_KEY

* 此 listenKey 不存在

#### 1127 MORE_THAN_XX_HOURS

* 查询的时间间隔太大
* 开始时间和结束时间之间超过 `%s` 小时 

#### 1128 OPTIONAL_PARAMS_BAD_COMBO

* 可选参数组合无效

#### 1130 INVALID_PARAMETER

* 发送的参数为无效数据
* 参数 `%s` 的数据是无效的

#### 1132 ORDER_PRICE_TOO_HIGH

* 订单的价格太高

#### 1133 ORDER_PRICE_TOO_SMALL

* 订单的价格小于最低限额，请检查通用的 broker 详情返回

#### 1134 ORDER_PRICE_PRECISION_TOO_LONG

* 订单的价格小数位数太长，请检查通用的 broker 详情返回

#### 1135 ORDER_QUANTITY_TOO_BIG

* 订单的数量太大

#### 1136 ORDER_QUANTITY_TOO_SMALL

* 订单的数量小于最低限额

#### 1137 ORDER_QUANTITY_PRECISION_TOO_LONG

* 订单的数量的小数位数太长

#### 1138 ORDER_PRICE_WAVE_EXCEED

* 订单价格超过了允许范围

#### 1139 ORDER_HAS_FILLED

* 订单已完成

#### 1140 ORDER_AMOUNT_TOO_SMALL

* 交易金额小于最低限额

#### 1141 ORDER_DUPLICATED

* 重复的客户订单ID

#### 1142 ORDER_CANCELLED

* 订单已被取消

#### 1143 ORDER_NOT_FOUND_ON_ORDER_BOOK

* 订单未在订单簿上

#### 1144 ORDER_LOCKED

* 订单已被锁定

#### 1145 ORDER_NOT_SUPPORT_CANCELLATION

* 此订单类型不支持取消订单

#### 1146 ORDER_CREATION_TIMEOUT

* 创建订单超时

#### 1147 ORDER_CANCELLATION_TIMEOUT

* 撤销订单超时

#### 2010 NEW_ORDER_REJECTED

* 新订单被拒绝

#### 2011 CANCEL_REJECTED

* 取消订单被拒绝

#### 2013 NO_SUCH_ORDER

* 订单不存在

#### 2014 BAD_API_KEY_FMT

* API-key 格式无效

#### 2015 REJECTED_MBX_KEY

* 无效的 API Key、IP 或操作权限

#### 2016 NO_TRADING_WINDOW

* 找不到该交易对的交易窗口。 尝试改为24小时自动报价

### 错误码 1010 ERROR_MSG_RECEIVED、 2010 NEW_ORDER_REJECTED 和 2011 CANCEL_REJECTED的错误信息

当撮合引擎返回一个错误时，返回的错误信息和对应描述：

报错信息 | 描述 
------------ | ------------
"Unknown order sent." | 订单 (无论是 `orderId`, `clOrdId`, `origClOrdId` 均未找到) 不存在 
"Duplicate order sent." | 此 `clOrdId` 已经被使用 
"Market is closed." | 此交易对无交易 
"Account has insufficient balance for requested action." | 没有足够的资金完成此次操作 
"Market orders are not supported for this symbol." | 此交易对未启用 `MARKET` 
"Iceberg orders are not supported for this symbol." | 此交易对未启用 `icebergQty` 
"Stop loss orders are not supported for this symbol." | 此交易对未启用  `STOP_LOSS` 
"Stop loss limit orders are not supported for this symbol." | 此交易对未启用 `STOP_LOSS_LIMIT` 
"Take profit orders are not supported for this symbol." | 此交易对未启用 `TAKE_PROFIT` 
"Take profit limit orders are not supported for this symbol." | 此交易对未启用 `TAKE_PROFIT_LIMIT` 
"Price* QTY is zero or less." | `price`* `quantity` 太小 
"IcebergQty exceeds QTY." | `icebergQty` 必须小于订单数量 
"This action disabled is on this account." | 联系客服；账户上的一些操作已被禁用 
"Unsupported order combination" | `orderType`, `timeInForce`, `stopPrice`, 和/或`icebergQty` 的组合不被允许 
"Order would trigger immediately." | 订单的止损价格与最后的交易价格相比是无效的 
"Cancel order is invalid. Check origClOrdId and orderId." | `origClOrdId` or `orderId`未传入 
"Order would immediately match and take." | `LIMIT_MAKER` 订单类型将立即匹配和交易，而不是一个纯粹的制造者订单 

### 9xxx  过滤器故障

报错信息 | 描述 
------------ | ------------
"Filter failure: PRICE_FILTER" | "价格"过高，过低和/或不遵循交易对的最小价格规则 
"Filter failure: LOT_SIZE" | "数量"太高，太低和/或不遵循该交易对的步长规则 
"Filter failure: MIN_NOTIONAL" | 价格*数量太低，无法成为该交易对的有效订单 
"Filter failure: MAX_NUM_ORDERS" | 客户在交易对上有太多挂单 
"Filter failure: MAX_ALGO_ORDERS" | 账户有太多未平仓止损和/或在交易对上执行获利指令 
"Filter failure: BROKER_MAX_NUM_ORDERS" | 账户在此 broker 上有过多未结订单. 
"Filter failure: BROKER_MAX_ALGO_ORDERS" | 帐户有太多止损挂单和/或在 broker 收取获利指令 
"Filter failure: ICEBERG_PARTS" | `Iceberg` 订单会分成太多部分； icebergQty太小。 
