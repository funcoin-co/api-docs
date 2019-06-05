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
```{'event':'addChannel','channel':'channelValue'} ```

其中
event: addChannel(注册请求数据)/removeChannel(注销请求数据)</br>
channel: 提供请求数据类型</br>

例如： ```websocket.send("{'event':'addChannel','channel':'sub_spot_tick_bch_btc_' })```  
### 服务器响应
返回数据格式为：```{"data": {},"channel": "channel"}``` </br>
其中
channel: 请求的数据类型
data: 返回结果数据

### 如何判断连接是否断开
通过心跳机制解决这个问题。客户端每30秒发送一次心跳数据：```{'event':'ping'}```，服务器会响应客户端：```{"event":"pong"}```以此来表明客户端和服务端保持正常连接。如果客户端未接到服务端响应的心跳数据则需要客户端重新建立连接。

## API参考
### 币币行情 API
#### 获取币币行情数据
##### 1. ```sub_spot_tick_X```   订阅行情数据

```websocket.send("{'event':'addChannel','channel':'ok_sub_spot_tick_X'}")```

X值为币对，如eth_btc

示例

```
# Request
{'event':'addChannel','channel':'sub_spot_tick_eth_btc'}
# Response
{
    "data": {
        "buy": "0.03108413",
        "high": "0.03119145",
        "last": "0.03113251",
        "low": "0.03113251",
        "sell": "0.03122003",
        "vol": "1889.7292",
        "timestamp": 1500444626000
    },
    "channel": "sub_spot_tick_eth_btc"
}
```

返回值说明

```
buy(string): 买一价
high(string): 24小时最高价
last(string): 最新成交价
low(string): 24小时最低价
sell(string): 卖一价
time(long)：时间戳
vol(string): 成交量(最近的24小时)
```

##### 2. ```sub_spot_depth_X ```   订阅深度数据

 ```websocket.send("{'event':'addChannel','channel':'sub_spot_depth_X'}")```
 
 X值为币对，如eth_btc
 
 示例
 
 ```
 # Request
 {'event':'addChannel','channel':'sub_spot_depth_eth_btc'}
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
        "time": 1559626731040
    },
    "channel": "sub_spot_depth_eth_btc"
}
 ```
 
 返回值说明
 
 ```
 bids([double, double]):买方深度
 asks([double, double]):卖方深度
 timestamp(string):服务器时间戳
 ```
 
##### 3. ```sub_spot_matched_X ```   订阅成交记录
```websocket.send("{'event':'addChannel','channel':'ok_sub_spot_matched_X'}")```

X值为币对，如eth_btc

示例

```
{
    "data": [
        {
            "amount": "0.3266",
            "matchType": 2,
            "price": "0.03114462",
            "ts": 1559627121382
        }
    ],
    "channel": "sub_spot_matched_eth_btc"
}
```

返回值说明

```
amount(string) 成交数量
matchType(integer) 订单类型 0-对手单 1-限价单 2-市场单
price(string): 成交价格
ts(long): 成交时间
```
