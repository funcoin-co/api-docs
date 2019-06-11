# REST API for SPOT 

## 开始使用    
REST，即Representational State Transfer的缩写，是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，正得到越来越多网站的采用。其优点如下：

在RESTful架构中，每一个URL代表一种资源；
客户端和服务器之间，传递这种资源的某种表现层；
客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。
建议开发者使用REST API进行币币交易或者资产提现等操作。

## 请求交互  

REST访问的根URL：https://www.funcoin.info/api/v1 所有请求基于Https协议。

请求交互说明

1. 请求参数：根据接口请求参数规定进行参数封装。
2. 提交请求参数：将封装好的请求参数通过POST 或GET 方式提交至服务器。
3. 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。
4. 数据处理：对服务器响应数据进行处理。

## 签名认证
### 签名说明
API 请求在通过 internet 传输的过程中极有可能被篡改，为了确保请求未被更改，除公共接口（基础信息，行情数据）外的私有接口均必须使用您的 API Key 做签名认证，以校验参数或参数值在传输途中是否发生了更改。每一个API Key需要有适当的权限才能访问相应的接口。每个新创建的API Key都需要分配权限。权限类型分为：只读，全部。在使用接口前，请查看每个接口的权限类型，并确认你的API Key有相应的权限。</br>
所有REST请求头都必须包含以下内容：</br>

* API访问秘钥(API-KEY): 您申请的API-KEY
* 签名方法（API-SIGNATURE-METHOD）：用户计算签名的基于哈希的协议，此处使用 HmacSHA256
* 签名版本（API-SIGNATURE-VERSION）：签名协议的版本，此处使用1
* 时间戳（API-TIMESTAMP）：您发出请求的时间 如：1560224934000。在查询请求中包含此值有助于防止第三方截取您的请求
* ID(API-UNIQUE-ID) : 除非另有说明，大多数标识符是UUID。当提出一个需要UUID的请求时，以下两个形式（有和没有破折号）都被接受。
132fb6ae-456b-4654-b4e0-d681ac05cea1或者132fb6ae456b4654b4e0d681ac05cea1
* 必选和可选参数：每个方法都有一组用于定义 API 调用的必需参数和可选参数。可以在每个方法的说明中查看这些参数及其含义。 请一定注意：对于 GET 请求，每个方法自带的参数都需要进行签名运算； 对于 POST请求，每个方法自带的参数不进行签名认证，即 POST 请求中需要进行签名运算的只有 API-KEY、API-SIGNATURE-METHOD、API-SIGNATURE-VERSION、API-TIMESTAMP 四个参数，其它参数放在 body中。
* 签名(API-SIGNATURE)：签名计算得出的值，用于确保签名有效和未被篡改

### 签名步骤
规范要计算签名的请求 因为使用 HMAC 进行签名计算时，使用不同内容计算得到的结果会完全不同。所以在进行签名计算前，请先对请求进行规范化处理。下面以查询某订单详情请求为例进行说明：</br>

API-SIGNATURE的请求头对API-KEY + API-SIGNATURE-METHOD + API-SIGNATURE-VERSION + API-TIMESTAMP + API-UNIQUE-ID以及API-SECRET使用HMACSHA256方法加密，通过BASE64编码输出而得到的。

1. 按顺序对请求头进行排序
	```
	API-KEY=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx
	API-SIGNATURE-METHOD=HmacSHA256
	API-SIGNATURE-VERSION=2
	API-TIMESTAMP=1560177359000
	API-UNIQUE-ID=9f6458ef-6e9b-4b16-9dd7-b0469a6f1839
	```
2. 如果是GET请求需要对参数进行签名运算, 对于POST则不需要
3. 按照以上顺序，将各参数使用字符 “&” 连接
	```
	API-KEY=e2xxxxxx-99xxxxxx-84xxxxxx-7xxxx&API-SIGNATURE-	METHOD=HmacSHA256&API-SIGNATURE-VERSION=2&API-	TIMESTAMP=1560177359000&API-UNIQUE-ID=9f6458ef-6e9b-4b16-9dd7-	b0469a6f1839&name=value
	```
4. 用上一步里生成的 “请求字符串” 和你的密钥 (Secret Key) 使用HMACSHA256方法加密，通过	BASE64编码生成一个数字签名

	```
	4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=
	```
5. 将生成的数字签名加入到请求头
	```
	API-SIGNATURE=4F65x5A2bLyMWVQj3Aqp+B4w+ivaA7n5Oi2SuYtCJ9o=
	```

## API参考

### 币币行情 API

#### 1. Get /api/v1/ticker   获取币币行情

URL ``` https://www.funcoin.info/api/v1/tick``` 访问频率 10次/1秒</br>

示例

```
# Request
GET https://www.funcoin.info/v1/api/tick?symbol=ETH/BTC
# Response
{
    "msg": null,
    "code": 0,
    "data": {
        "tick": {
            "buy": "0.0304",
            "high": "0.0317",
            "last": "0.0305",
            "low": "0.0304",
            "sell": "0.0307",
            "vol": "918.9128",
            "symbol": "ETH/BTC"
        },
        "timestamp": 1560180150404
    },
    "reqType": null,
    "success": true
}
```

返回值说明

```
buy: 买一价
high: 最高价
last: 最新成交价
low: 最低价
sell: 卖一价
vol: 成交量(最近的24小时)
symbol: 币对
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如ETH/BTC

#### 2. Get /v1/api/depth 获取币币市场深度
URL ``` https://www.funcoin.info/api/v1/depth``` 访问频率 10次/1秒</br>

示例

```
# Request
GET https://www.funcoin.info/api/v1/depth?symbol=ETH/BTC
# Response
{
    "msg": null,
    "code": 0,
    "data": {
        "asks": [
            {
                "price": 0.0307,
                "amount": 0.3855
            },
            {
                "price": 0.0307,
                "amount": 0.3855
            }
        ],
        "bids": [
            {
                "price": 0.0304,
                "amount": 0.4635
            },
            {
                "price": 0.0307,
                "amount": 0.3855
            }
        ],
        "timestamp": 1560181302726
    },
    "reqType": null,
    "success": true
}
```

返回值说明

```
asks :卖方深度
bids :买方深度
asks和bids中的price为价格amount为数量
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如ETH/BTC

#### 3. Get /v1/api/matched 获取币币交易信息(50条)
URL ``` https://www.funcoin.info/api/v1/matched``` 访问频率 10次/1秒</br>

示例

```
# Request
GET https://www.funcoin.info/api/v1/matched?symbol=eth_btc
# Response
{
    "msg": null,
    "code": 0,
    "data": [
        {
           "ts": 1560177909565,
            "amount": "0.3119",
            "price": "0.0306",
            "matchType": 1,
            "pairNmae": "ETH/BTC"
        }
    ],
    "reqType": null,
    "success": true
}
```

返回值说明

```
ts: 时间戳
amount: 数量
price: 价格
matchType: 订单类型 0-对手单 1-限价单 2-市场单
pairName: b币对名
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如ETH/BTC

#### 4. Get /v1/api/kline 获取币币K线数据

URL ``` https://www.funcoin.info/api/v1/kline``` 访问频率 10次/1秒
示例

```
# Request
GET https://www.funcoin.info/api/v1/kline?symbol=ETH/BTC&type=2&size=150&since=1558927444000
# Response
{
    "msg": null,
    "code": 0,
    "data": [
        {
            "klineName": "ETH/BTC",
            "open": 0.03122779,
            "high": 0.03127838,
            "low": 0.03115542,
            "close": 0.03115542,
            "volume": 25.4848,
            "time": 1558791900000,
            "amount": 0.79568608628        }
    ],
    "reqType": null,
    "success": true
}
```

返回值说明

```
klineName: k线所对应的交易对名
open: 开
high: 高
low: 低
close: 收
volume: 交易量
time: 当前K线对应的时间戳
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如ETH/BTC 
type | Integer | 是 |  K线单位 1-1分钟K线 2-15分钟K线 3-1小时K线 4-1天K线 
size | Integer | 是 | 指定获取数据的条数
since | Long | 是 | 时间戳，返回该时间戳以后的数据(例如1558927444000)

### 币币交易 API

#### 下单
币币交易提供限价单和市价单两种下单模式(更多下单模式将会在后期支持)。只有当您的账户有足够的资金才能下单。
一旦下单，您的账户资金将在订单生命周期内被冻结。被冻结的资金以及数量取决于订单指定的类型和参数。</br>

##### HTTP请求
POST /api/v1/order

URL ``` https://www.funcoin.info/api/v1/order``` 访问频率 10次/1秒

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如eth_btc 
orderAmount | String | 是 |  下单数量 
insertPrice | String | 是 | 下单价格
insertType | Integer | 是 | 参数填数字 1-限价单 2-市价单
direction | Integer | 是 | 参数填数字 1-卖单 2-买单
localOrderId | String | 是 | 用户自己生成的一个唯一ID

返回值

```
{
    "msg": null,
    "code": 0,
    "data": 668683405973103, // 订单Id
    "reqType": null,
    "success": true
}
```

#### 批量下单
下指定币对的多个订单</br>

##### HTTP请求
POST /v1/batchOrder

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如eth_btc 
orderAmount | String | 是 |  下单数量 
insertPrice | String | 是 | 下单价格
insertType | Integer | 是 | 参数填数字 1-限价单 2-市价单
direction | Integer | 是 | 参数填数字 1-卖单 2-买单
localOrderId | String | 是 | 用户自己生成的一个唯一ID

示例

```
# Request
GET https://www.funcoin.info/api/v1/batchOrder

{
    "msg": null,
    "code": 0,
    "data": [
        {
            "symbol": "ETH/BTC",
            "insertPrice": 0.03122779,
            "orderAmount": 0.03127838,
            "insertType": 1,
            "direction": 2,
            "localOrderId": 25.4848      
        },
        {
            "symbol": "ETH/BTC",
            "insertPrice": 0.03122779,
            "orderAmount": 0.03127838,
            "insertType": 1,
            "direction": 2,
            "localOrderId": 25.4848       
        }

    ],
    "reqType": null,
    "success": true
}

# Response
{
    "msg": null,
    "code": 0,
    "data": [668683405973103, 668683405973104, 668683405973105], // 订单Id
    "reqType": null,
    "success": true
}
```
#### 撤单
撤销之前下的未完成订单，支持批量撤单

##### HTTP请求
POST /v1/cancel

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
orderId | String | 是 | 订单Id，多个订单每个Id之间用 "," 进行分割
 
 示例

```
# Request
GET https://www.funcoin.info/api/v1/cancel
{
	"orderId": "668683405973103, 668683405973104, 668683405973105"
}
# Response
{
    "msg": null,
    "code": 0,
    "data": [668683405973103, 668683405973104, 668683405973105], // 订单Id
    "reqType": null,
    "success": true
}
```
注：建议用户调用批量撤单接口后，再调用获取订单列表接口确认已撤销成功。

#### 获取个人订单列表
列出您当前的订单信息。这个请求支持分页，并且按时间倒序排序和存储。</br>
GET /v1/orders

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对
isComplete | Boolean | 是 | 订单是否已经完成
pageIndex | Integer | 否 | 分页页码 默认查询第一页
pageSize | Integer | 否 | 分页每页数量 默认查询50条，最多一次查询100条。
 
 示例

```
# Request
GET https://www.funcoin.info/api/v1/orders?symbol=ETH/BC&isComplete=true&pageIndex=1&pageSize=50

# Response
{
    "msg": null,
    "code": 0,
    "data": [{
		        "totalAmount":"0.05",
		        "orderType":1,
		        "price":"5.4979",
		        "assetName":"LTC/BTC",
		        "orderDirection":2,
		        "id":95480628981602,
		        "unfilledAmount":"0.05",
		        "status":8
    		},
			    {
			     "totalAmount":"0.05",
			     "orderType":1,
			     "price":"5.4979",
			     "assetName":"LTC/BTC",
			     "orderDirection":2,
			     "id":110351929982744,
			     "unfilledAmount":"0.05",
			     "status":8
		 }], 
    "reqType": null,
    "success": true
}
```

##### 返回值说明
参数名 | 参数类型 |  描述 
:-: | :-: | :-: 
totalAmount | String | 委托数量
orderType | Integer | 订单类型 1-限价单 2-市价单
price | String | 委托价格
orderDirection | Integer | 买卖方向 1-卖 2-买
id | Long | 订单Id
unfilledAmount | String | 未成交数量
status | Integer | 3-部分撤销 4-全部撤销 8-挂单 9-部分成交 10-全部成交

##### 注：部分撤销、挂单、部分成交算作未完成订单，全部撤销与全部成交为已完成订单

#### 查看个人资产
获取币币账户单个币种的余额、冻结和可用等信息。</br>
GET /v1/capital

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
currency | String | 否 | 币种 如：BTC。不传查询全部有效币种

 示例

```
# Request
GET https://www.funcoin.info/api/v1/capital?currency=ETH

# Response
{
    "msg": null,
    "code": 0,
    "data": [
				    {
				        "availableCash":99993.12876403,
				        "accountId":"2",
				        "settleCurrencyID":"BTC",
				        "accountType":2,
				        "frozenCash":5.223005
				    }
			] 
    "reqType": null,
    "success": true
}
```
##### 返回值说明

参数名 | 参数类型 |  描述 
:-: | :-: | :-: 
availableCash | double | 可用数量
accountId | Integer | 币种Id
settleCurrencyID | String | 币种
frozenCash | double | 冻结数量
















