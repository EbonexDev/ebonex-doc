## 账务接口

### 下单
```shell
POST /openapi/finance_support/v1/order
```
	
**参数:** 

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| symbol     | long   | 必填     | 交易币对                             |   BTC   |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |
| accountId     | long   | 必填     | 账户ID                               |   430831063768557312   |
| side     | string   | 必填     | 买卖方向  BUY=买单 SELL=卖单                             |   6001   |
| type     | string   | 必填     | 订单类型 LIMIT=限价单 MARKET=市价单                              |   6001   |
| quantity     | string   | 必填     | 下单数量                               |   6001   |
| price     | string   | 必填     | 下单价格                               |   6001   |
| clientOrderId     | string   | 必填     | 客户端订单ID                               |   6001   |


**响应:**
略

### 查询订单
```shell
GET /openapi/finance_support/v1/order
```
	
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |
| accountId     | long   | 必填     | 账户ID                               |   430831063768557312   |
| orderId     | string   | 选填     | 订单ID                             |   478799682544402176   |
| clientOrderId     | string   | 选填     | 客户端订单ID                              |   1570987398733   |


**响应:**
略

### 撤单
```shell
DELETE /openapi/finance_support/v1/order
```
	
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |
| accountId     | long   | 必填     | 账户ID                               |   430831063768557312   |
| orderId     | string   | 选填     | 订单ID                             |   478799682544402176   |
| clientOrderId     | string   | 选填     | 客户端订单ID                              |   1570987398733   |


**响应:**
略

### 查询未成交委托
```shell
GET /openapi/finance_support/v1/openOrders
```
	
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |
| accountId     | long   | 必填     | 账户ID                               |   430831063768557312   |
| symbol     | string   | 选填     | 币对ID                             |   BTCUSDT   |
| orderId     | string   | 选填     | 查询起始订单ID 默认为0                             |   0   |
| limit     | integer   | 选填     |  查询每页条数 默认为20                             |   20   |


**响应:**
略

### 创建财务账户
```shell
POST /openapi/finance_support/v1/account
```
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |


**响应:**
```json
{
  "id": 1,
  "orgId": 6001,
  "userId": 10000,
  "accountId": "430831063768557312",
  "accountType": 0,
  "accountIndex": 0,
  "createType": 0
}
```

### 获取所有财务账户列表
```shell
GET /openapi/finance_support/v1/accounts
```
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |


**响应:**
```json
[
    {
      "id": 1,
      "orgId": 6001,
      "userId": 10000,
      "accountId": "430831063768557312",
      "accountType": 0
    }
]
```

### 查询账户信息
```shell
GET /openapi/finance_support/v1/account
```

**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| orgId     | long   | 必填     | 券商机构ID                               |   6001   |
| accountId     | long   | 必填     | 账户ID                               |   430831063768557312   |


**响应:**
略

### 财务账户互转
```shell
GET /openapi/finance_support/v1/transfer
```
	
**参数:**

| 参数            | 类型   | 是否必填 | 描述                                   | 示例值 |
| :-------------- | :----- | :------- | :------------------------------------- | :--- |
| sourceAccountId     | long   | 必填     | 转账源账户ID                               |   430831063768557312   |
| sourceOrgId     | long   | 必填     | 转账源券商机构ID                               |   6001  |
| targetAccountId     | long   | 必填     | 转账目标账户ID                               |   478799768536022784   |
| targetOrgId     | long   | 必填     | 转账目标券商机构ID                              |   6002  |
| amount     | string   | 必填     | 转账金额                              |   0.25  |
| tokenId     | string   | 必填     | 转账的币种                              |   USDT  |


**响应:**
略