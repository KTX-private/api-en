# User Data Streams


## Overview


> Example

```javascript
const CryptoJS = require("crypto-js");
const WebSocket = require('ws');
const ktxws = 'wss://u-stream.ktx.com';
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";
const sign = CryptoJS.HmacSHA256("/user/verify", secretKey).toString();

let wsClass = function () {
};


wsClass.prototype._initWs = async function () {
  let that = this;
  console.log(ktxws);

  let ws = new WebSocket(ktxws);
  that.ws = ws;

  ws.on('open', function open() {
    console.log(new Date(), 'open')
    ws.send(JSON.stringify({
      "method": "LOGIN",
      "auth": {
        "api-key": apiKey, "api-sign": sign,
      }
    }));
    // Send heartbeat every 5-10 seconds
    setInterval(function () {
      ws.send(JSON.stringify({"ping": Date.now()}))
    }, 5000)
  });

  ws.on('close', data => {
    console.log('close, ', data);
  });

  ws.on('error',  data => {
    console.log('error ',data);
  });

  ws.on('ping', data => {
    console.log('ping ', data.toString('utf8'));
  });

  ws.on('pong', data => {
    console.log('pong ', data.toString('utf8'));
  });

  ws.on('message', data => {
    console.log(data.toString()) // the data may be is error message,check the data's stream is order or account
  });
};

let instance = new wsClass();

instance._initWs().catch(err => {
  console.log(err);
});

```

```python
import websocket
import hashlib
import hmac
import json
import threading
import time
from datetime import datetime

ws_url = 'wss://u-stream.ktx.com'
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'
SIGN = hmac.new(SECRET_KEY.encode("utf-8"), "/user/verify".encode('utf-8'), hashlib.sha256).hexdigest()

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
    ws.send(json.dumps({
        "method": "LOGIN",
        "auth": {
            "api-key": API_KEY,
            "api-sign": SIGN,
        }
    }))
    print("### opened ###")
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
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;
import java.util.concurrent.TimeUnit;

public class KtxWsExample {
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }

    public static void main(String[] args) throws Exception {
        String sign = hmacSha256("/user/verify", SECRET_KEY);
        WebSocket ws = WebSocket.newBuilder()
                .uri(URI.create("wss://u-stream.ktx.com"))
                .buildAsync(Listener.of(
                        (webSocket, text, last) -> {
                            System.out.println(text);
                            return CompletionStage.completedStage(null);
                        }
                )).join();
        String login = "{\"method\":\"LOGIN\",\"auth\":{\"api-key\":\"" + API_KEY + "\",\"api-sign\":\"" + sign + "\"}}";
        ws.sendText(login, true);

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

Use Websocket push service to obtain account balance and delegation changes information in a timely manner.

**Connect to WebSocket server**

Please use the following URL to connect to the Websocket server:

wss://u-stream.ktx.com

**Please attach the following HTTP request header when connecting**

* api-key
* api-sign
* api-expire-time

*For specific methods, please refer to the [Authentication](#authentication) chapter*

## Heartbeat

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

> Data flow
> After successfully establishing a connection, the client will receive information and commission change information of the balance of the account of the APIKEY account. The format is as follows:

```json
{
  "stream": "account",
  "data": { Account }
}

{
  "stream": "order",
  "data": { Order }
}

{
  "stream": "order",
  "data": { Position }
}
```


## Account


**When the account balance changes, you will receive an account event**

```json
{
  "stream": "account",
  "data":  {
    "asset":"USDT",  // Asset code
    "balance":"100",  // Total amount
    "locked":"0",  // Freeze amount
    "free":"100",  //  Available Amount
    "withdrawable":"100",// Transferable
    "collateral":false,// Is collateral [true: Yes | false: No]
    "discountForMargin":"1", // Margin discount rate [0: Unavailable | 0.5: 50% | 1: 100%]
    "discountForFee":"1" // Fee discount rate [0: Unavailable | 0.5: 50% | 1: 100%]
  }
}
```


## Position


**When the position information is sent to change, you will receive the Position event**

```json
{
  "stream": "position",
  "data": {
  "id": "1125899906842624003", // position ID
  "symbol": "BTC_USDT_SWAP", // Trading pair code
  "side":"long", // Position direction [long: Long | short: Short]
  "quantity": "0.1", // quantity
  "entryPrice":"0", // Average price for opening positions
  "mergeMode": "long", // Position merge mode [long: Merge long | short: Merge short]
  "marginMethod":"isolate",// Margin mode [isolate: Isolated | cross: Cross]
  "leverage":"10.0", // Leverage
  "initMargin":"0.1", // Start margin rate
  "maintMargin": "0.005", // Maintain the margin rate
  "posMargin": "0", // Press margin
  "orderMargin":"1009.8990000", // Entrustment deposit
  "closableQty": '0' // closable quantity
  
}
```

## Order


**When the delegation changes, the order event will be received**

```json
{
"stream": "order",
"data":{
      "orderId": "4611767382287843330", // Order ID
      "clientOrderId": "", // Client Order ID
      "createTime": "1733390630904", // Creation time
      "Product": "BTC_USDT_SWAP", // Trading pair code
      "type": "limit", // Order type [limit: Limit | market: Market | take-profit: Take profit | stop: Stop | take-profit-limit: Take profit limit | stop-limit: Stop limit]
      "side": "buy", // Trading direction [buy: Buy | sell: Sell]
      "quantity": "0.01", // Quantity
      "stf": "disabled", // Self-trading prevention [0: disabled Disable self-trade prevention | 1: dc Decrease and Cancel | 2: co Cancel Oldest | 3: cn Cancel Newest | 4: cb Cancel Both]
      "price": "10300", // Order price
      "timeInforce": "gtc", // Time in force [gtc: Good till cancel | ioc: Immediate or cancel | fok: Fill or kill]
      "mini":"false", // Is mini contract [true: Yes | false: No]
      "cancelAfter": 0, // Cancel after N seconds [>0: Cancel after N seconds | 0: Never auto-cancel]
      "postOnly": false, // Post only [true: Yes | false: No]
      "positionMerge": "long", // Position merge mode [long: Merge long | short: Merge short | none: Split]
      "positionId": 0, // Submitted position id
      "marginMethod": "cross", // Margin mode [isolate: Isolated | cross: Cross]
      "close": false, // Is close position [true: Close | false: Open]
      "leverage": 0, // Leverage multiple
      "action": "unknown", // Position behavior [unknown: Unknown | increase_long: Open Long | reduce_long: Close Long | increase_short: Open Short | reduce_short: Close Short]
      "status": "Filled", // Order status [accepted: Accepted | partial-filled: Partial Filled | filled: Filled | cancelled: Cancelled | rejected: Rejected | partially-cancelled: Partially Cancelled]
      "executedQty": "0.01", // Executed quantity
      "profit": "0", // return
      "origin":0, // Origin [when origin=-1, this order is a liquidation order]
      "markPrice": "10000", // When origin=-1, represents the mark price at the time of liquidation
      "brokerId":0, // broker id
      "update_id":'1125899907137993336', // update id
      "executedCost": "103", // The transaction value has
      "fillCount": 1, // Number of transactions
      "fills": [// transaction details
        {
          "tradeId": 1,
          "time": "1733390650379",
          "price": "10300",
          "quantity": "0.01",
          "profit": "0",
          "taker": false, // Is Taker [true: Yes | false: No]
          "fees": [
            {
              "amount": "0.103", // Number of assets
              "asset": "USDT", // Asset code
              "value": "0.103" // Valuation
            }
          ]
        }
      ],
      "fees": [// handle fee
        {
          "amount": "0.103", // Number of assets
          "asset": "USDT", // Asset code
          "value": "0.103" // Valuation
        }
      ],
      "updateTime": "1733390650379" // Update time
  }
}
```

