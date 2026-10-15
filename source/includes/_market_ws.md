# Market Data Streams


## Overview


> Example

```javascript
const WebSocket = require('ws');
const ktxws = 'wss://m-stream.ktx.com';

let wsClass = function () {
};

wsClass.prototype._initWs = async function () {
    let that = this;
    console.log(ktxws)
    let ws = new WebSocket(ktxws);
    that.ws = ws;

    ws.on('open', function open() {
        console.log(new Date(), 'open')
        ws.send(JSON.stringify({"method":"SUBSCRIBE","params":["spot.BTC_USDT.order_book.5"]}));
        // Send heartbeat every 5-10 seconds
        setInterval(function () {
          ws.send(JSON.stringify({"ping": Date.now()}))
        }, 5000)
    });

    ws.on('close', data => {
        console.log('close, ', data);
    });

    ws.on('error', data => {
        console.log('error', data);
    });

    ws.on('ping', data => {
        console.log('ping ', data.toString('utf8'));
    });

    ws.on('pong', data => {
        console.log('pong ', data.toString('utf8'));
    });

    ws.on('message', data => {
        console.log("rece message")
        console.log(data)
    });
};

let instance = new wsClass();

instance._initWs().catch(err => {
    console.log(err);
});

```

```python
import websocket
import json
import threading
import time
from datetime import datetime

ws_url = 'wss://m-stream.ktx.com'

def stringify(obj):
    return json.dumps(obj, sort_keys=True).replace("\'", "\"").replace(" ", "")


def get_sub_str():
    subdata = {"method":"SUBSCRIBE","params":["spot.BTC_USDT.order_book.5"]} 
    return stringify(subdata)


def on_message(ws, message):
    print(message)


def on_error(ws, error):
    print(error)


def on_close(ws, close_status_code, close_msg):
    print("### closed ###")
    print("Status Code:", close_status_code)
    print("Message:", close_msg)

def ping_loop(ws):
  while True:
    time.sleep(5)  # Recommended: send heartbeat every 5-10 seconds

    data = {
      "ping": int(datetime.now().timestamp() * 1000)
    }
    ws.send(json.dumps(data))

def on_open(ws):
    ws.send(get_sub_str())
    threading.Thread(target=ping_loop, args=(ws,), daemon=True).start()


def connect():
    # websocket.enableTrace(True)
    ws = websocket.WebSocketApp(ws_url,
                                on_message=on_message,
                                on_error=on_error,
                                on_close=on_close)
    ws.on_open = on_open
    ws.run_forever(ping_interval=0)


if __name__ == "__main__":
    connect()

```

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class KtxWsExample {
    public static void main(String[] args) throws Exception {
        WebSocket ws = WebSocket.newBuilder()
                .uri(URI.create("wss://m-stream.ktx.com"))
                .buildAsync(Listener.of(
                        (webSocket, text, last) -> {
                            System.out.println(text);
                            return CompletionStage.completedStage(null);
                        }
                )).join();
        ws.sendText("{\"method\":\"SUBSCRIBE\",\"params\":[\"spot.BTC_USDT.order_book.5\"]}", true);

        // Send heartbeat every 5-10 seconds
        ScheduledExecutorService scheduler = Executors.newSingleThreadScheduledExecutor();
        scheduler.scheduleAtFixedRate(() -> {
            String ping = "{\"ping\":" + System.currentTimeMillis() + "}";
            ws.sendText(ping, true);
        }, 5, 5, TimeUnit.SECONDS);

        Thread.sleep(60000);
    }
}
```

**Use the WebSocket push service to get the market information in time.**

* Connect to the WebSocket server
  Please use the following URL to connect to the Websocket server:
  <br/>
  wss://m-stream.ktx.com

> After connecting, the client can send the following JSON format request to the server

```json
{
"id": 123, // Request ID given by the client
"method": "SUBSCRIBE", // Request type
"params":["spot.BTC_USDT.order_book.5"]
}
```

> After receiving the request, the server will send the following json format response to the client

```json
{
  "result": "success", // Result
  "op":"SUBSCRIBE",
  "id": 123, // Request ID given by the client
  "events":["spot.BTC_USDT.order_book.5"]
}
```

> If an error occurs, the server will send the following error message to the client

```json
{
"id": 123, // Request ID
"error": -1003, // Error code
"message": "..." // Error description
}
```

> At the same time, the server will also send the following JSON format data stream to the client, which contains information about market changes

```json
{
"stream": "spot.BTC_USDT.order_book.5", // Data flow name
"data": ... // Data
}
```

> Request: Subscribe data stream

```json
{
  "id": 1,
  "method": "SUBSCRIBE",
  "params": [
  "stream name",
  "stream name",
    ...
   ]
}
```

> After the connection, please send the request to the server first, and then the server will send the corresponding data stream to the client when the market changes.

> "Data Stream name" is the name of the data stream, and the data stream name is a string in the following format.
> market.symbol.data_type.param1.param2...

> Where, market is a trading-to-market, such as spot and lpc
> Symbol is the name of the trading pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
> Data_type is a data type, and currently only supports the following data types
> Order_Book: Depth
> trades: Trade list
> candles: K-line
> ticker: Latest transaction information

> After data_type is the parameter list, different data types have different parameter lists, these will be introduced in the following context

> Request: Cancel the subscription data stream

```json
{
  "id": 1,
  "method": "UNSUBSCRIBE",
  "params": [
  "data stream name",
  "data stream name", 
    ... 
  ]
}
```

> If the request is properly handled by the server, the client will receive the following response:

```json
{
  "result": "success", // Back results
  "op":"SUBSCRIBE"
}
```

> If the request is wrong, the client will receive the following error response:

```json
{
"error": -1003, // Error code
"message": "..." // Error description
}
```


## Request Methods


> Request type and parameter

> The client can send the following requests to the server

```json
{
  "id": 123, // Request ID given by the client
  "method": "..." // Request type
  "params": [// Request parameter list
    "...",
    "...",
]
}
```

* Where the value of the method field is one of the following request types:


| Optional Values | Description |
| ------------- | ----------------------------------------------------------------------------------------------------------- |
| SUBSCRIBE | 1. Subscribe to data flow<br/> 2. Parameters are data flow name list <br/> 3. After a successful subscription, the server will send data flow to the client when the market changes |
| UNSUBSCRIBE | 1. Cancel the subscription data stream <br/> 2. The parameter is the list of data stream names <br/> 3. After successfully canceling the subscription, the client will no longer receive the corresponding data flow |


## Subscribe Order Book


**Subscribe in-depth information**

> Send the following request to subscribe to the in-depth information

```json
{
  "id": 123,
  "method": "SUBSCRIBE",
  "params": [
  "spot.BTC_USDT.order_book.20",
  "spot.ETH_USDT.order_book.20",
    ...
]
}
```

* Parameters

1. The parameter of the request is the depth stream name, and the format is as follows:

* \<market>.\<symbol>.order_book.\<max_depth>
1. \<market> is a trading-to-market, such as spot, lpc
2. \<symbol> is the name of the trading pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
3. \<max_depth> is the maximum depth, the effective value is 5, 10, 20, 50, 100, 200, 500, 1000

> Data flow

```json
{
"stream": "spot.BTC_USDT.order_book.20",
"data": {
    "i": 1027024, // update id
      "t": "1644558642100", // update time
      "b": [// Buy the market
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ],,,
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ],,,
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ]
      ...
    ],
      "a": [// Selling the disk
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ],,,
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ],,,
      [
        "46125.7", // The commission price
        "0.079045" // quantity
      ]
      ...
    ]
  }
}
```

> After successful subscriptions, the client will first receive a complete depth of data flow, and then receive an incremental change data flow. Please follow the following methods to synthesize the complete depth, or use SDK.
"

```json
  ...
```


## Subscribe Trades


**Subscribe to transaction list**

> Send the following request to subscribe to the transaction list

```json
{
  "id": 123,
  "method": "SUBSCRIBE",
  "params": [
  "Spot.Btc_usdt.trades",
  "spot.ETH_USDT.trades",
      ...
  ]
}
```

* Parameter

1. The parameter of the request is the transaction flow name, and the format is as follows:

* \<market>.\<symbol>.trades
1. \<market> is a transaction to the market, such as spot, lpc
2. \<symbol> is the name of the trading pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.

> Data flow

```json
{
"stream": "spot.BTC_USDT.trades",
"data":  {
    "i": 17122255, // Transaction ID
      "p": "46125.7", // The transaction price
      "q": "0.079045", // Transaction volume
      "s": 1, // Taker direction [1: Buy | -1: Sell]
      "t": "1628738748319" // Transaction time
  },
...
}
```


## Subscribe K-line


**Subscribe to K-line**

> Send the following request to subscribe to the K-line

```json
{
  "id": 123,
  "method": "SUBSCRIBE",
  "params": [
  "spot.BTC_USDT.candles.1m",
  "spot.ETH_USDT.candles.1h",
      ...
  ]
}
```

* Parameter

1. The K-line stream name format is as follows:

* \<market>.\<symbol>.candles.\<time_frame>
1. \<market> is a transaction to the market, such as spot, lpc
2. \<symbol> is the name of the trading pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
3. \<time_frame> is the K-line period, the effective value is 1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 6h, 12h, 1d, 1w, 1M
> Data flow

```json
{
"stream": "spot.BTC_USDT.candles.1m",
"data": {
"t":60000, // Time period
"e":[
      [
        "1644224940000", // start time
        "10190.53", // Opening price
        "10192.5", // The highest price
        "9806.82", // Minimum price
        "10127.37", // Close price
        "0.834", // Trading volume
        "8370.40506", // transaction value
        "1", // The ID of the first transaction
        278 // Total transactions in the interval
      ],
      [
        "1644224940000",
        "10190.53",
        "10192.5",
        "9806.82",
        "10127.37",
        "0.834",
        "8370.40506",
        "1",
        278
      ]
    ]
  }
}
```


## Subscribe Tickers


**Subscribe Tickers**

> Send the following request to subscribe to Ticker

```json
{
  "id": 123,
  "method": "SUBSCRIBE",
  "params": [
  "spot.BTC_USDT.ticker",
  "spot.ETH_USDT.ticker",
    ...
  ]
}
```

* Parameters

1. The Ticker stream name format is as follows:

* \<market>.\<symbol>.ticker
1. \<market> is a trading-to-market, such as spot, lpc
2. \<symbol> is the name of the trading pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
> Data flow

```json
{
"stream": "spot.BTC_USDT.ticker",
  "data": {
    "askPrice": "98100", // Ask price
    "product": "BTC_USDT", // Trading pair
    "amount": "922635", // 24h trading value
    "last": "98000", // Latest transaction price
    "firstTradeId": 1, // First trade ID
    "change": "0", // 24h price change
    "bidQty": "1.7", // Bid quantity
    "bidPrice": "98000", // Bid price
    "volume": "9.41", // 24h trading volume
    "lastQty": "0.3", // Last trade quantity
    "askQty": "0.5", // Ask quantity
    "high": "98100", // 24h highest price
    "tradeCount": 30, // Number of trades
    "low": "98000", // 24h lowest price
    "time": "1733474204000", // Time
    "open": "98000" // Opening price
    }
}
```

## MarketData Heartbeat

The client needs to send heartbeat messages periodically to maintain the connection. If the server does not receive a heartbeat message from the client for more than **30 seconds**, it will actively disconnect.

It is recommended to send a heartbeat message every **5-10 seconds**.

> Heartbeat request format

```json
{"ping": 1785220808575}
```

> The server will respond with

```json
{"pong": 1785220808575}
```

---
