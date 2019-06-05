#REST API for SPOT 
## 开始使用    
REST，即Representational State Transfer的缩写，是目前最流行的一种互联网软件架构。它结构清晰、符合标准、易于理解、扩展方便，正得到越来越多网站的采用。其优点如下：

在RESTful架构中，每一个URL代表一种资源；
客户端和服务器之间，传递这种资源的某种表现层；
客户端通过四个HTTP指令，对服务器端资源进行操作，实现“表现层状态转化”。
建议开发者使用REST API进行币币交易或者资产提现等操作。

##请求交互  
REST访问的根URL：https://www.funcoin.info/v1/api    所有请求基于Https协议，请求头信息中contentType需要统一设置为：application/x-www-form-urlencoded

请求交互说明

1. 请求参数：根据接口请求参数规定进行参数封装。
2. 提交请求参数：将封装好的请求参数通过POST 或GET 方式提交至服务器。
3. 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。
4. 数据处理：对服务器响应数据进行处理。

##API参考
###币币行情 API
#### 1. Get /api/v1/ticker   获取币币行情

URL ``` https://www.funcoin.info/v1/api/ticker``` 访问频率 10次/1秒
示例

```
# Request
GET https://www.funcoin.info/v1/api/ticker?symbol=eth_btc
# Response
{
    "msg": null,
    "code": 0,
    "data": {
        "time": 1559559392541,
        "ticker": {
            "buy": "0.03080092",
            "high": "0.03104369",
            "last": "0.03085112",
            "low": "0.03085112",
            "sell": "0.03090731",
            "vol": "2225.8210"
        }
    },
    "reqType": null,
    "success": true
}
```

返回值说明

```
time: 返回数据时服务器时间
buy: 买一价
high: 最高价
last: 最新成交价
low: 最低价
sell: 卖一价
vol: 成交量(最近的24小时)
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如eth_btc 

#### 2. Get /v1/api/depth 获取币币市场深度
URL ``` https://www.funcoin.info/v1/api/depth``` 访问频率 10次/1秒
示例

```
# Request
GET https://www.funcoin.info/v1/api/depth?symbol=eth_btc
# Response
{
    "msg": null,
    "code": 0,
    "data": {
        "asks": {
            "0.03097917": 0.4806,
            "0.03090658": 0.4441,
            "0.03093624": 0.1444,
            "0.03090731": 0.2431,
            "0.03096072": 0.2003,
            "0.03094287": 0.3439
        },
        "bids": {
            "0.03080115": 0.4882,
            "0.03076679": 0.3059
        },
        "time": 1559559559476
    },
    "reqType": null,
    "success": true
}
```

返回值说明

```
asks :卖方深度
bids :买方深度
asks和bids中的key为价格value为数量
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如eth_btc 

#### 3. Get /v1/api/matched 获取币币交易信息(50条)
URL ``` https://www.funcoin.info/v1/api/matched``` 访问频率 10次/1秒
示例

```
# Request
GET https://www.funcoin.info/v1/api/matched?symbol=eth_btc
# Response
{
    "msg": null,
    "code": 0,
    "data": [
        {
            "ts": 1559559797049,
            "amount": "0.2091",
            "price": "0.03086302",
            "matchType": 2
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
```

请求参数

参数名 | 参数类型 | 必填 | 描述 
:-: | :-: | :-: | :-: 
symbol | String | 是 | 币对如eth_btc 

#### 4. Get /v1/api/kline 获取币币K线数据

URL ``` https://www.funcoin.info/v1/api/kline``` 访问频率 10次/1秒
示例

```
# Request
GET https://www.funcoin.info/v1/api/kline?symbol=ETH/BTC&type=2&size=150&since=1558927444000
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
            "amount": 0.79568608628        
        }
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
symbol | String | 是 | 币对如eth_btc 
type | Integer | 是 |  K线单位 1-1分钟K线 2-15分钟K线 3-1小时K线 4-1天K线 
size | Integer | 是 | 指定获取数据的条数
since | Long | 是 | 时间戳，返回该时间戳以后的数据(例如1558927444000)
