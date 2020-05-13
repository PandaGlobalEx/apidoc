# Panda Global 平台API

| **修改人** | **修改时间** | **修改说明**                          |
| ---------- | ------------ | ------------------------------------- |
| Arthur     | 2019-09-25   | 文档初始                              |
| Arthur     | 2019-11-15   | 接口数据结构变化                      |
| Arthur     | 2020-04-16   | 增加最新成交接口                      |
| Arthur     | 2020-05-10   | 增加获取24小时行情,获取交易对基本信息 |

# 目录

**一、访问环境**

**二、签名认证**

**三、枚举定义**

**四、返回数据基础格式**

**五、API接口**

​		公开数据接口

​				1.获取历史K线接口

​				2.获取盘口信息

​				3.获取币种折算率

​				4.获取交易列表

​				5.获取最新成交

​				6.获取24小时行情

​				7.获取交易对基本信息

​		私有数据接口

​				1.查询账户信息

​				2.创建买单

​				3.创建卖单

​				4.撤单（根据订单ID）

​				5.查询当前订单状态

​				6.查询当前订单详情

​				7.查询历史订单

​				8.查询当前订单

 

# 一、访问环境

 

| **环境** | **环境地址**                   | **备注** |
| -------- | ------------------------------ | -------- |
| 测试     | http://api-test.panda.co:30186 |          |
| 预发     | https://api-stg.panda.co       |          |
| 生产     | https://api.panda.co           |          |

 

# 二、签名认证

### 签名说明

1.公有接口无需签名和带请求头

2.对于私有接口，除了要传私有接口本身参数外，还需传如下参数到请求头中。

| **参数名**       | **数据类型** | **描述**                                       |
| ---------------- | ------------ | ---------------------------------------------- |
| AccessKeyId      | String       | API访问Id，平台分配的Access Key                |
| SignatureMethod  | String       | 签名方法, 此处固定使用HmacSHA256               |
| SignatureVersion | String       | 签名版本，此处固定使用2                        |
| Timestamp        | String       | 当前时间戳（单位毫秒）                         |
| Signature        | String       | 签名，计算得出的值，用于确保签名有效和未被篡改 |

### 签名步骤

​	·  请求方法【GET】或【POST】，转换为大写，与请求路径使用换行符【\n】连接

​	·   请求路径URI【/api/v2/order/orders/place/buy】，与请求参数之间使用【?】连接。

​	·   请求参数按照ASCII码顺序进行排序后，参数名与参数值使用【=】连接，多个参数之间使用【&】连接，空参数不参与签名。

​	·   以上数据使用 UTF-8 编码后，以 hmacSha256 方式加密得到签名数据。

#### 示例

\r\nAccessKeyId：75sA0dBfYy8Q8rQB
\r\n签名密钥：ujvqPfRmV1eG2x75
​	·  请求方法：POST
​	·  请求路径：/api/v2/order/orders/place/buy
​	·  请求参数：
​	·  	排序前：symbol=bamb_usdt&marketed=0&amount=10.00&price=0.85
​	·  	排序后：amount=10.00&marketed=0&price=0.85&symbol=bamb_usdt

​	·  完整的签名字符串：POST\n/api/v2/order/orders/place/buy?amount=10.00&marketed=0&price=0.85&symbol=bamb_usdt
​	·  签名结果：2ID+43RHv1buyUr0zIKxhno6NMfqPhrY50JxRdbaJhc=



# 三、枚举定义

#### K线间隔周期

​		1min: 一分钟

​		5min: 五分钟

​		15min: 15分钟

​		30min: 30分钟

​		60min: 1小时

​		4hour: 4小时

​		1day: 1天

​		1week: 1星期

​		1mon: 1个月

​		1year: 1年

#### 订单状态

​		1:未成交

​		2:部分成交

​		3:完全成交

​		4:撤单处理中

​		5:已撤销 



# 四、返回数据基础格式

{
        "code": 0,
        "message": "操作成功",
        "data": Object,
	"success":true
 } 

#### 返回值说明

| **参数名** | **数据类型** | **描述**                    |
| ---------- | ------------ | --------------------------- |
| code       | Integer      | 返回的状态码，0表示请求成功 |
| message    | String       | 消息描述                    |
| data       | Object       | 返回详情数据                |
| success    | Boolean      | 是否成功                    |

 



# 五、API接口

## 公开数据接口

### 1.获取历史K线接口

​		GET /api/v2/market/history/kline

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**                                   |
| ---------- | ------------ | ------------ | ---------- | ------------------------------------------ |
| period     | String       | true         |            | 周期间隔（具体查看：枚举定义-K线周期间隔） |
| begin      | Long         | true         |            | 开始时间,单位秒                            |
| end        | Long         | false        |            | 结束时间,默认查询100条数据                 |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt                       |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": [{
		"id": 1586933460,
		"timestamp": 1586933460,
		"amount": "0",
		"count": 0,
		"open": "4.5",
		"close": "4.5",
		"low": "4.5",
		"high": "4.5",
		"vol": "0"
	}],
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述**       |
| ---------- | ------------ | -------------- |
| timestamp  | Long         | 时间戳，单位秒 |
| id         | Long         | 唯一标识       |
| amount     | String       | 计价货币成交量 |
| count      | Integer      | 成交笔数       |
| open       | String       | 开盘价         |
| close      | String       | 收盘价         |
| low        | String       | 最低价         |
| high       | String       | 最高价         |
| vol        | String       | 成交量         |

 

### 2.获取盘口信息

​		GET /api/v2/market/depth

#### 请求参数 

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**             |
| ---------- | ------------ | ------------ | ---------- | -------------------- |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": [{
		{
			"buy": [
				[
					81289, 		//价格
					0.0256		//数量
				],
				...
			],
			"sell": [
				[
					81324,		//价格
					0.019146	//数量
				],
				...
			]
		}]
	}，
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述** |
| ---------- | ------------ | -------- |
| buy        | List         | 买盘口   |
| sell       | List         | 卖盘口   |



### 3.获取币种折算率

GET /api/v2/market/exchange

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**        |
| ---------- | ------------ | ------------ | ---------- | --------------- |
| currency   | String       | true         |            | 币种，例如:bamb |

#### 返回值

{
 "code": 0,
 "message": "操作成功",
 "data": [{
            "currency": "BAMB",
            "priceUSD": 0.07346,
            "priceCNY": 0.5113,
      "priceUSDT": 0.07346
 ]},

"success":true
 }

#### 返回值说明

| **参数名** | **数据类型** | **描述**   |
| ---------- | ------------ | ---------- |
| currency   | String       | 币种       |
| priceUSD   | String       | 美元价格   |
| priceCNY   | String       | 人民币价格 |
| priceUSDT  | String       | USDT价格   |

 

### 4.获取交易列表

​		GET /api/v2/market/tradeSummaryList

#### 无请求参数

#### 返回值

{
    "code":0,
    "message":"操作成功",
    "data":[
        {
            "symbol":"bamb_usdt",
            "high":"6980",
            "vol":"43840.969259408004",
            "last":"6893.83",
            "low":"6758",
            "buy":"6893.82",
            "sell":"6893.83",
            "rose":"-0.2"
        }
    ],
    "success":true
}


#### 返回值说明

| **参数名** | **数据类型** | **描述**             |
| ---------- | ------------ | -------------------- |
| symbol     | String       | 币对，例如:bamb_usdt |
| high       | String       | 最高价               |
| vol        | String       | 交易量               |
| last       | String       | 最新成交价           |
| low        | String       | 最低价               |
| buy        | String       | 买价格               |
| sell       | String       | 卖价格               |
| rose       | String       | 涨幅                 |



### 5.获取最新成交

​		GET /api/v2/market/trades

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**                           |
| ---------- | ------------ | ------------ | ---------- | ---------------------------------- |
| limit      | Integer      | false        | 20         | 获取条数,默认查询20条数据,最大值60 |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt               |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": [{
		"ts": 1586914972285,
		"id": 733257336504610817,
		"amount": "30.00000000",
		"price": "0.0266",
		"direction": "buy"
	}],
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述**             |
| ---------- | ------------ | -------------------- |
| ts         | Long         | 时间戳，单位毫秒     |
| id         | Long         | 唯一标识             |
| amount     | String       | 成交量               |
| price      | String       | 成交价               |
| direction  | String       | 方向(buy=买,sell=卖) |



### 6.获取24小时行情

​		GET /api/v2/market/ticker

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**             |
| ---------- | ------------ | ------------ | ---------- | -------------------- |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt |

#### 返回值

{
        "code": 0,
        "message": "操作成功",
        "data": {
               "id": 1589166000,
               "symbol": "bamb_usdt",
               "high": "0.000766",
               "low": "0.000711",
               "amount": "50533241",
               "vol": "50533241",
               "count": 14312,
               "last": "0.000752",
               "rose": "-1.18",
               "buy": "0.00071",
               "sell": "0.000767"
        },
        "success": true
 }

#### 返回值说明

| **参数名** | **数据类型** | **描述**                   |
| ---------- | ------------ | -------------------------- |
| id         | Long         | 唯一标识                   |
| symbol     | String       | 币对，例如:bamb_usdt       |
| high       | String       | 最高价（以滚动24小时计）   |
| vol        | String       | 成交额（以滚动24小时计）   |
| last       | String       | 最新成交价                 |
| low        | String       | 最低价（以滚动24小时计）   |
| rose       | String       | 涨跌幅                     |
| amount     | String       | 成交量（以滚动24小时计）   |
| count      | Integer      | 交易次数（以滚动24小时计） |
| buy        | String       | 当前最新买一价             |
| sell       | String       | 当前最新卖一价             |

###  

### 7.获取交易对基本信息

​		GET /api/v2/market/symbols

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**                                    |
| ---------- | ------------ | ------------ | ---------- | ------------------------------------------- |
| symbols    | String       | false        | 查询所有   | 币对,可多个逗号分隔,例如:bamb_usdt,btc_usdt |

#### 返回值

{
        "code": 0,
        "message": "操作成功",
        "data": [{
               "tradeCurrency": "bamb",
               "quoteCurrency": "usdt",
               "symbol": "bamb_usdt",
               "minCount": "0.01",
               "maxCount": "999999",
               "minPrice": "0.0001",
               "maxPrice": "999999",
               "tradePrecision": 4,
               "quotePrecision": 7,
               "hasShare": true
        }],
        "success": true
 }

#### 返回值说明

| **参数名**     | **数据类型** | **描述**                                                     |
| -------------- | ------------ | ------------------------------------------------------------ |
| tradeCurrency  | String       | 交易币                                                       |
| quoteCurrency  | String       | 计价币                                                       |
| symbol         | String       | 币对                                                         |
| minCount       | String       | 最小下单量                                                   |
| maxCount       | String       | 最大下单量                                                   |
| minPrice       | String       | 最小价格                                                     |
| maxPrice       | String       | 最大价格                                                     |
| minMoney       | String       | 最小额度限制（限价单为：下单时的 price * amount, 市价买为：下单时的 amount） |
| tradePrecision | Integer      | 交易币精度（小数点后位数）                                   |
| quotePrecision | String       | 计价币精度（小数点后位数）                                   |
| hasShare       | Boolean      | 是否可交易                                                   |



##  

## 私有数据接口

### 1.查询账户信息

​		POST /api/v2/account/accounts

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**           |
| ---------- | ------------ | ------------ | ---------- | ------------------ |
| type       | String       | true         |            | 类型，固定填写spot |

 

#### 返回值

{
 "code": 0,
 "message": "操作成功",
 "data": [{
        "currency": "bamb", 
        "tradeBalance": "0.07346",
        "frozenBalance": "0.5113" 
  }],

"success":true
 }

#### 返回值说明

| **参数名**    | **数据类型** | **描述** |
| ------------- | ------------ | -------- |
| currency      | String       | 币种     |
| tradeBalance  | String       | 可用余额 |
| frozenBalance | String       | 冻结金额 |

 

### 2.创建买单

​		POST /api/v2/order/orders/place/buy

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**                          |
| ---------- | ------------ | ------------ | ---------- | --------------------------------- |
| marketed   | Integer      | false        | 0          | 是否按照市场价买入(0=市价,1=限价) |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt              |
| amount     | String       | true         |            | 数量                              |
| price      | String       | false        |            | 单价,限价必填                     |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": 10096,
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述** |
| ---------- | ------------ | -------- |
| data       | Long         | 订单ID   |

 

### 3.创建卖单

​		POST /api/v2/order/orders/place/sell

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**                          |
| ---------- | ------------ | ------------ | ---------- | --------------------------------- |
| marketed   | Integer      | false        | 0          | 是否按照市场价卖出(0=市价,1=限价) |
| symbol     | String       | true         |            | 币对，例如:bamb_usdt              |
| amount     | String       | true         |            | 数量                              |
| price      | String       | false        |            | 单价,限价必填                     |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": 10096,
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述** |
| ---------- | ------------ | -------- |
| data       | Long         | 订单ID   |

 

### **4.撤单（根据订单ID）**

​		POST /api/v2/order/orders/cancel

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述** |
| ---------- | ------------ | ------------ | ---------- | -------- |
| id         | Long         | true         |            | 订单ID   |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": null,
	"success": true
}



### 5.查询当前订单状态

​		GET /api/v2/order/orders/status

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述** |
| ---------- | ------------ | ------------ | ---------- | -------- |
| id         | Long         | true         |            | 订单ID   |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": 1,
	"success": true
}

#### 返回值说明

| **参数名** | **数据类型** | **描述**                                |
| ---------- | ------------ | --------------------------------------- |
| data       | Integer      | 订单状态（具体查看：枚举定义-订单状态） |



### 6.查询当前订单详情

​		GET /api/v2/order/orders/info

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述** |
| ---------- | ------------ | ------------ | ---------- | -------- |
| id         | Long         | true         |            | 订单ID   |

**返回值**

{
	"code": 0,
	"message": "操作成功",
	"data": {
		"id": 1589166000,
		"symbol": "bamb_usdt",
		"high": "0.000766",
		"low": "0.000711",
		"amount": "50533241",
		"vol": "50533241",
		"count": 14312,
		"last": "0.000752",
		"rose": "-1.18",
		"buy": "0.00071",
		"sell": "0.000767"
	},
	"success": true
}

#### 返回值说明

| **参数名**    | **数据类型** | **描述**                                |
| ------------- | ------------ | --------------------------------------- |
| id            | Long         | 订单ID                                  |
| ordersn       | Long         | 订单编码                                |
| userId        | Integer      | 用户ID                                  |
| status        | Integer      | 订单状态（具体查看：枚举定义-订单状态） |
| type          | Integer      | 订单类型(0=买,1=卖)                     |
| last          | String       | 成交价格                                |
| lastCount     | String       | 成交总数                                |
| price         | String       | 下单价格                                |
| count         | String       | 下单数量                                |
| amount        | String       | 下单总价                                |
| successAmount | String       | 成交总价                                |
| leftCount     | String       | 未成交数量                              |
| fees          | String       | 手续费                                  |
| leftFees      | String       | 剩余手续费                              |
| lastUpdatTime | Date         | 更新时间                                |
| createTime    | Date         | 创建时间                                |
| tradeCoin     | String       | 交易币种                                |
| unitCoin      | String       | 计价币种                                |

 

### 7.查询历史订单

​		GET /api/v2/order/orders/list/history

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**            |
| ---------- | ------------ | ------------ | ---------- | ------------------- |
| symbol     | String       | true         |            | 币对，例如:btc_usdt |
| pageNum    | Integer      | false        | 1          | 开始页              |
| pageSize   | Integer      | false        | 10         | 查询条数            |

#### 返回值

{
	"code": 0,
	"message": "操作成功",
	"data": {
		"currentPage": 1,
		"pageSize": 100,
		"totalRows": 674869,
		"totalPages": 6749,
		"data": [{
			"id": 2546041,
			"ordersn": 733344689155108865,
			"userId": 18,
			"status": 3,
			"type": 1,
			"last": "0.000747",
			"lastCount": "0.00",
			"price": "0.000747",
			"count": "493.98",
			"amount": "0.36900306",
			"successAmount": "0.36900306",
			"leftCount": "0.00",
			"fees": "0.00000442",
			"leftFees": "0.00000000",
			"tradeCoin": "BAMB",
			"unitCoin": "USDT",
			"createTime": 1586935799000,
			"lastUpdatTime": 1586935798831
		}]
	},
	"success": true
}

#### 返回值说明

| **参数名**                                                   | **数据类型** | **描述** |
| ------------------------------------------------------------ | ------------ | -------- |
| totalRows                                                    | Integer      | 总记录数 |
| pageSize                                                     | Integer      | 查询条数 |
| currentPage                                                  | Integer      | 当前页   |
| totalPages                                                   | Integer      | 总页数   |
| **data**                                                     |              |          |
| data部分返回字段与编号为私有接口【6】的返回字段相同，此处略。 |              |          |



### 8.查询当前订单

​		GET /api/v2/order/orders/list/current

#### 请求参数

| **参数名** | **数据类型** | **是否必需** | **默认值** | **描述**            |
| ---------- | ------------ | ------------ | ---------- | ------------------- |
| symbol     | String       | true         |            | 币对，例如:btc_usdt |
| pageNum    | Integer      | false        | 1          | 开始页              |
| pageSize   | Integer      | false        | 10         | 查询条数            |

**返回值**

{
	"code": 0,
	"message": "操作成功",
	"data": {
		"currentPage": 1,
		"pageSize": 2,
		"totalRows": 2,
		"totalPages": 1,
		"data": [{
			"id": 2546020,
			"ordersn": 733344262023966720,
			"userId": 18,
			"status": 1,
			"type": 0,
			"last": "0.000000",
			"lastCount": "120.00",
			"price": "0.000710",
			"count": "120.00",
			"amount": "0.08520000",
			"successAmount": "0.00000000",
			"leftCount": "120.00",
			"fees": "0.00000000",
			"leftFees": "0.00000000",
			"tradeCoin": "BAMB",
			"unitCoin": "USDT",
			"createTime": 1586935697000,
			"lastUpdatTime": 1586935697000
		}]
	},
	"success": true
}

#### 返回值说明

| **参数名**                                                   | **数据类型** | **描述** |
| ------------------------------------------------------------ | ------------ | -------- |
| totalRows                                                    | Integer      | 总记录数 |
| pageSize                                                     | Integer      | 查询条数 |
| currentPage                                                  | Integer      | 当前页   |
| totalPages                                                   | Integer      | 总页数   |
| **data**                                                     |              |          |
| data部分返回字段与编号为私有接口【6】的返回字段相同，此处略。 |              |          |
