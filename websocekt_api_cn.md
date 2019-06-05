## 开始使用    
WebSocket是HTML5一种新的协议(Protocol)。它实现了客户端与服务器全双工通信，使得数据可以快速地双向传播。通过一次简单的握手就可以建立客户端和服务器连接，服务器根据业务规则可以主动推送信息给客户端。其优点如下：

客户端和服务器进行数据传输时，请求头信息比较小，大概2个字节；
客户端和服务器皆可以主动地发送数据给对方；
不需要多次创建TCP请求和销毁，节约宽带和服务器的资源。
强烈建议开发者使用WebSocket API获取市场行情和买卖深度等信息。

## 请求交互
币币交易WebSocket服务连接地址：```wss://www.funcoin.info/websocket/v1```
### 发送请求
请求数据格式为：</br>
```{"op": "<value>", "args": ["<value1>","<value2>"]} ```

其中 </br>
op 的取值为 1--```subscribe``` 订阅； 2-- ```unsubscribe`` 取消订阅 ；</br>
args: 取值为频道名，可以定义一个或者多个频道

例如： ```websocket.send({"op": "<value>", "args": ["<value1>","<value2>"]})```  
### 服务器响应
返回数据格式为：```{"channel":"channel","data":"[{"<value1>","<value2>"}]"}``` </br>
其中
channel: 请求的数据类型
data: 返回结果数据

### 如何判断连接是否断开
通过心跳机制解决这个问题。客户端每30秒发送一次心跳数据：```{'event':'ping', 'timestamp':1559745131000}```，服务器会响应客户端：```{"event":"pong", 'timestamp':1559745131000}```以此来表明客户端和服务端保持正常连接。如果客户端未接到服务端响应的心跳数据则需要客户端重新建立连接。

## API参考
### 币币行情 API
#### 获取币币行情数据
##### 1. 订阅行情数据

示例

```
# Request
{"op": "subscribe", "args": ["spot/tick:ETH-BTC"]}
 其中spot/tick为频道名，ETH-BTC 为交易对
# Response
{
    "data":{
        "buy":"0.03097809",
        "high":"0.03168968",
        "last":"0.03118127",
        "low":"0.03088498",
        "sell":"0.03113244",
        "timestamp":1559744803470,
        "vol":"2203.6484"
    },
    "channel":"spot/tick:ETH-BTC"
}
```

返回值说明

```
buy(string): 买一价
high(string): 最高价
last(string): 最新成交价
low(string): 最低价
sell(string): 卖一价
time(long)：时间戳
vol(string): 成交量(最近的24小时)
```

##### 2。 订阅深度数据
 
 示例
 
 ```
 # Request
 {"op": "subscribe", "args": ["spot/depth:ETH-BTC"]}
  其中spot/depth为频道名，ETH-BTC 为交易对
 # Response
 {
    "data": {
        "asks": {
            0.03123714: 0.2414,
            0.03125415: 0.3286,
            0.03127627: 0.3874,
            0.03124671: 0.2258
        },
        "bids": {
            0.03109433: 0.3827,
            0.03106093: 0.4069,
            0.03096551: 0.3366,
            0.03107458: 0.3521,
            0.03107702: 0.4242
        },
        "timestamp": 1559626731040
    },
    "channel": "spot/depth:ETH-BTC"
}
 ```
 
 返回值说明
 
 ```
 bids([double, double]):买方深度
 asks([double, double]):卖方深度
 timestamp(string):服务器时间戳
 ```
 
##### 3. 订阅成交记录
获取最近的成交数据, 首次会推送最近的50条, 后续为增量。

示例

```
# Request
{"op":"subscribe", "args":["spot/matched:ETH-BTC"]}
 其中spot/matched为频道名，ETH-BTC 为交易对
# Response
{
    "data": [
        {
            "amount": "0.3266",
            "matchType": 2,
            "price": "0.03114462",
            "ts": 1559627121382
        }
    ],
    "channel": "spot/matched:ETH-BTC"
}
```

返回值说明

```
amount(string) 成交数量
matchType(int) 订单类型 0-对手单 1-限价单 2-市场单
price(string): 成交价格
ts(long): 成交时间
```
##### 4. 订阅K线数据
获取现货的K线数据 </br>
频道列表：</br>
spot/kline1   // 1分钟k线数据 </br>
spot/kline15  // 15分钟k线数据频道 </br>
spot/kline60  // 1小时k线数据频道 </br>
spot/klineD   // 1day k线数据频道 </br>

示例

```
# Request
{"op": "subscribe", "args": ["spot/kline1:ETH-BTC"]}
 其中spot/kline1为频道名，ETH-BTC 为交易对
# Response
{
    "data":[
        {
            "amount":0.043005987856,
            "close":0.03121352,
            "high":0.03121352,
            "klineId":1,
            "klineName":"ETH/BTC",
            "lastDealTime":1559746473014,
            "low":0.03121352,
            "open":0.03121352,
            "resolution":1,
            "time":1559746440000,
            "volume":1.3778
        }
    ],
    "channel":"spot/kline1:ETH-BTC"
}
```

返回值说明

```
amount(double)    成交数量
close(double)     收盘价格
high(double)      最高价格
klineId(int)	  K线对应交易对ID
klineName(string) K线对应交易对
lastDealTime(long) 最新成交时间
low(double)       最低价格
open(double)	  开盘价格
resolution(int)   K线频道 1-1分钟 2-15分钟 3-60分钟 4-1天
time(long)	      当前K线周期时间戳
volume(double)    成交量
```

