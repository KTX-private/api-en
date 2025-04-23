---
title: Madex API doc

language_tabs:
- javascript
- python

toc_footers:
- <a href = 'https://www.ktx.com'> Start Trading </a>

includes:
- errors

search: true

code_clipboard: true

---
# Basic information

## API

**Use API to develop applications, you can accurately obtain market data of Madex spot market and quickly conduct automated trading. The API contains many interfaces, which are roughly divided into the following groups according to their functions:**

* Market Data Endpoints is used to obtain the REST interface of the market data
* User Data Endpoints is used to obtain the REST interface of the user's private data
* Market Data Streams WebSocket interface used to obtain market data
* User Data Streams is used to obtain the WebSocket interface that obtains the user's private data

**API uses the following Base URL:**

* Market Data Endpoints: https://api.ktx.com/api
* User Data Endpoints: https://api.ktx.com/api
* Market Data Stream: wss://m-stream.ktx.com
* User Data Stream: wss://u-stream.ktx.com

**Add API domain name list**

[https://api.ktx.com/api/v1/pu/domains](https://api.ktx.com/api/v1/pu/domains)

**API's REST interface uses the following http method:**

* GET is used to obtain market or private data

* POST is used to submit delegates and other operations
  **The parameters required for the REST interface of the API should be attached to the request according to the following rules:**

* The interface parameters of GET type should be attached to Query String

* The interface parameters of POST type should be attached to the Request Body in JSON format
  **Response**

The API's response data is returned in JSON format. For the specific format, please refer to the description of each interface.

**Error**

The error of the API is returned in the following JSON format:

{<br/>
&nbsp;&nbsp;"state": error code,<br/>
&nbsp;&nbsp;"msg": "error message"<br/>
}

*Where, state represents the type of error, msg contains the cause of the error or how to avoid the error prompt. For specific error types, please refer to the [Error](#errors) chapter.*

**Time or Timestamp**

The time values involved in the API interface parameters and response data are UNIX time, and the unit is milliseconds.

---

## Traffic restriction

**Madex The following access restrictions on the request from the same IP:**

1. Access Limits access frequency limit
2. Usage Limits CPU usage limit

* Access Limits access frequency limit
1. At the same IP, the request of up to 10,000 times per 10 seconds will receive-20007 errors that exceed restricted requests.
2. Users can send up to 10,000 requests at any frequency within 10 seconds. They can send about once every 10ms, or they can be sent 10,000 times in 1 second, and then wait for 9 seconds.
   <br/>
   <br/>
* USAGE LIMITs limit
1. The same IP consumes up to 10,000 CPU time every 10 seconds. Requests that exceed the limit will receive a -20006 error.
2. Different APIs consume different CPU time, which depends on how the API accesses the data.
3. In this article, the method of accessing the data of each API interface will be marked in the form of "cache" and "database". The CPU consumed by the cache API has less time, and the CPU consumed by the API consumed by the database is more time. According to the parameters sent by the user, the API may mix the cache and database, and even access the database multiple times, which will increase the CPU time consumed by the API.
4. The CPU time consumed by the API request will be included in Madex-USAGE in response header. The format is T1: T2: T3. Among them, T1 represents the CPU time consumed by the API request. T2 represents the current IP within 10 seconds in the last 10 seconds The CPU time consumed, T3 indicates that the current IP remains available CPU time in the last 10 seconds.

---

## Authentication

> Complete Example

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString(); // POST or DELETE  replace queryStr with bodyStr
const url = `${endpoints}/v1/accounts?${queryStr}`;

request.get(url,{
          headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime
          },
        },

        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }
          console.log(body) // 7.the result

        });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/accounts'
    query_str = 'asset=BTC'
    expire_time = str(int(time.time() * 1000) + 5000)
    # POST or DELETE replace query_str with body_str
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time': expire_time
    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

**Identity verification**

* Private interfaces are used to access accounts such as accounts and commissioned private information. When request, additional signatures need to be added to meet Madex for authentication. This section will describe how to create signatures.

**Generate API Key**

* To create a signature, you need to generate a combination of API Key and Secret Key. Please keep in mind the Secret Key generated in this process, because the value is only displayed once. If you forget the Secret Key, delete the API Key and generate a new API Key and Secret Key combination.

**http request header**

**After requests to access private interfaces, the following HTTP request header must be attached:**

* API Key that has been generated by API-Key
* API-SIGN signature

**If necessary, you can also add the following http request head:**

* api-expire-time
1. Interface expiration time.
2. This value is a UNIX time in milliseconds. The server will ignore the request received after the time, which is mainly used to avoid the impact of network delay.

**Create a signature**

Before sending the request, first determine the message used for the signature. For GET type requests, Query String is the message body that needs to be signed, and for POST requests, Body String is the message body that needs to be signed. Expire_time represents the expiration time. The specific method of signing is as follows:

* Step 1: Get the current timestamp and set expiration time
  Use Date.now() to obtain the current timestamp in milliseconds, and add a short validity period (e.g., 5000 milliseconds) to indicate that the request will expire in 5 seconds.
* Step 2: Generate the signature
  Concatenate [timestamp + request body string] as the original data, and use the HMAC-SHA256 algorithm to encrypt it with the user's secret as the key.
* Step 3: Convert the result to a hex string
  Convert the HMAC result into a hexadecimal string format.
* Step 4: Add headers
 Use the hex string as the value for the api-sign header, and set the expiration timestamp as the value for the api-expire-time header.

## Api Key Permissions

**Private interface requires specific permissions to execute. Appropriate authority can be granted for API Key. If the API Key is not awarded the permissions required by an interface, the request submitted to the API Key will be rejected.**

**You can grant API Key below permissions:**

* View permissions allow API Key to obtain private data.
* Trade permissions allow the API Key to submit or revoke the commission and allow the API Key to obtain data related data.

*The permissions required by the interface will be given in the description of each interface. *

---

# Market Data Endpoints



## Get Listed Pairs

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/products?market=spot&symbol=BTC_USDT`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/products?market=spot&symbol=BTC_USDT'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "ID": 5 // ID
    "market": "lpc", // spot orlpc spot or contract
    "Symbol": "BTC_USDT_SWAP", // Transaction pair
    "takerFee": "0.001", // Taker handling fee
    "Makerfee": "0.001", // Maker's handling fee
    "minOrderSize": "0.0001", // Minimum order quantity
    "maxOrderSize": "10000000", // Maximum order quantity
    "quantityScale": 4, // Quantity accuracy
    "priceScale": 4, // Price accuracy
    "minOrderValue": "0.0001", // Minimum order value
    "maxOrderValue": "1000000000" // Maximum order value
  }
]
```

**Get the currency list**

* Request method get
* Request path /v1/products
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
|--------| ---------- |------|-----------------------------------------|
| market | string | Yes | trading pair markets, such as spot, lpc, etc., spot is spot, lpc is U-standard contract |
| Symbol | String | No | Transaction to code, such as BTC_USDT, ETH_USDT, etc.

* Data source

Cache

## Get Order Book

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/order_book?market=spot&symbol=BTC_USDT`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/order_book?market=spot&symbol=BTC_USDT'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
do_request()
```

> Response

```json
{
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
```

**Get in-depth data**

* Request method get
* Request path /v1/order_book
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
| ------------- | ---------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| market | string | Yes | trading pair markets, such as spot, lpc, etc., spot is spot, lpc is U-standard contract |
| Symbol | String | Yes | Trading code, such as BTC_USDT, ETH_USDT, etc. |
| Level | int32 | No | How many level depth is specified? <br/> Effective value 1, 2, 5, 10, 20, 50, 100, 500, 1000 <br/> The default value 100 |
| Price_scale | Integer | No | Specify the depth of the price by the price, such as the price of the specified transaction pair contains up to 4 digits <br/> Price_scale = 0 The price returned to the price up to 4 digits, <br/> price_scale = 1 to return to return. The price contains a maximum of 3 decimal numbers. The entrusted measurement is the price range of the price range 0.0010 and the price of <br/> price_scale = 2 to 2 contains up to 2 decimal numbers. br/> price_scale = 3 to 3 to include a maximum number of decimal numbers. The sum of all delegations in 1.0000<br/>Valid values ​​0, 1, 2, 3, 4, 5<br/>Default value 0 |

> Note: The data are sorted by the best price, that is, the buy side depth is sorted from large to small, and the sell side depth is sorted from small to large

* Data source

Cache

## Get Candles

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/candles?market=spot&symbol=BTC_USDT&time_frame=1m`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/candles?market=spot&symbol=BTC_USDT&time_frame=1m'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
{
"t": 60000, // Time cycle
"e": [
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
```

**Get K-line data**

* Request method get
* Request path /v1 /candles
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|----------------| ---------- | ---------- | ---------------------------------------------------------------------------------------- |
| market         | String | Yes | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract |
| symbol         | String | Yes | Trading code, such as BTC_USDT, ETH_USDT, etc. |
| time_frame     | String | Yes | The time cycle of the K -line data <br/> The valid value is 1m, 3m, 5m, 15m, 30m, 1H, 2H, 4H, 6H, 12H, 1d, 3D, 1W or 1m |
| before         | int64 | No | utc time<br/>Limit the latest time of return to the K-line record |
| after          | int64 | No | UTC Time <br/> Limited to return the earliest time of the K -line records |
| limit          | Integer | No | Get the maximum number of K -line records <br/> The default value is 100, the maximum value is 1000 |

* The parameter combination and data source supported by the interface

1. symbol + time_frame  --> cache
2. symbol + time_frame + limit  --> cache
3. symbol + time_frame + before  --> database
4. symbol + time_frame + before + limit  --> database
5. symbol + time_frame + after  --> database
6. symbol + time_frame + after + limit  --> database

> Return results from early and nearly sorted by time

## Get Trades

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/trades?market=spot&symbol=BTC_USDT`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/trades?market=spot&symbol=BTC_USDT'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
do_request()
```

> Response

```json
[
  {
  "i": 17122255, // Transaction ID
  "p": "46125.7", // The transaction price
  "q": "0.079045", // Transaction volume
  "s": "1", // Taker's transaction direction 1 represents buy -1 representative sells
  "t": "1628738748319" // Transaction time
  },
  {
    "i": 17122254, // Transaction ID
    "p": "46125", // The transaction price
    "q": "0.079047", // Transaction volume
    "s": "1", // Taker's transaction direction 1 represents buy -1 representative sells
    "t": "1628738748318" // Transaction time
  }
  ...
]
```

**Get the transaction record**

* Request method get
* Request path /v1 /trades
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|----------------| ---------- | ---------- | ---------------------------------------------- |
| market         | String | Yes | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract |
| symbol         | string | Yes | Transaction pair codes, such as BTC_USDT, ETH_USDT, etc. |
| start_time     | int64 | No | The earliest time of limited returning transaction records |
| end_time       | int64 | No | Limited recent time of returning transaction records |
| before         | int64 | No | Transaction record id<br/> Limited to return the maximum id of the transaction record |
| after          | int64 | No | Trading record ID <br/> Limit the maximum ID of returning transaction records |
| limit          | Integer | No | The maximum number of obtaining records <br/> The default value is 100, the maximum value is 1000 |

* Parameter combinations and data sources supported by this interface

1. symbol  --> cache
2. symbol + limit  --> cache
3. symbol + start_time  --> database
4. symbol + start_time + limit  --> database
5. symbol + end_time  --> database
6. symbol + end_time + limit  --> database
7. symbol + start_time + end_time  --> database
8. symbol + start_time + end_time + limit  --> database
9. symbol + before  --> database
10. symbol + before + limit  --> database
11. symbol + after  --> database
12. symbol + after + limit  --> database

*The parameter combination of the data source is Cache to obtain the last 1,000 transaction records*

*The parameter combination of the data source is DataBase to obtain earlier transaction records*

*If you use the parameter combination of the data source as database to obtain the latest transaction record, the result will be slightly delayed than the cache data source*
* Usage
  **Usage Example: Get all the transaction records of a transaction pair within three months**

1. First use the symbol + limit parameter combination to obtain the latest transaction record
2. Tradeid the first recorded as the value of the BeFore parameter, use Symbol + BeFore + Limit parameter combination to get more records, until the all transaction records within three months stop stopped stopped stopping

> Return results sorted from small to large by transaction record id

## Get Tickers

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/ticker?market=spot&symbol=BTC_USDT`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/ticker?market=spot&symbol=BTC_USDT'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
  "askPrice": "98100", // Sell for one price
  "product": "BTC_USDT", // Transaction pair
  "amount": "922635", // 24 transaction value
  "Last": "98000", // The latest transaction price
  "firstTradeId": 1, // The first transaction id
  "change": "0", // Price changes
  "bidQty": "1.7", // Sell a quantity
  "bidPrice": "98000", // Buy one price
  "volume": "9.41", // 24h transaction quantity
  "Lastqty": "0.3", // 24h latest transactions
  "askqty": "0.5", // Sell a number
  "high": "98100", // The highest price of 24
  "tradeCount": 30, // Number of transactions
  "Low": "98000", // 24h the lowest price
  "time": "1733474204000", // time
  "open": "98000" // Opening price
  }
]
```

**Get the quotation data**

* Request method get
* Request path /v1 /ticker
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                                                              |
|----------------| ---------- | ---------- |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | String | Yes | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract                                                                                                 |
| symbol         | String | Yes | Trading code, such as BTC_USDT, ETH_USDT, etc. <br/> You can specify multiple transactions in the following two forms <br/> 1.symbol=BTC_USDT,ETH_USDT |

* Data Source

Cache

# Market Data Streams

## Overview

> Example

```javascript
const WebSocket = require('ws');
const madexws = 'wss://m-stream.ktx.com';

let wsClass = function () {
};

wsClass.prototype._initWs = async function () {
    let that = this;
    console.log(madexws)
    let ws = new WebSocket(madexws);
    that.ws = ws;

    ws.on('open', function open() {
        console.log(new Date(), 'open')
        ws.send(JSON.stringify({"method":"SUBSCRIBE","params":["spot.BTC_USDT.order_book.5"]}));
        setInterval(function () {
          ws.ping(Date.now())
        },30000)
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
    time.sleep(30)

    data = {
      "ping": datetime.now().timestamp() * 1000
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

**Use the WebSocket push service to get the market information in time.**

* Connect to the WebSocket server
  Please use the following URL to connect to the Websocket server:
  <br/>
  wss://m-stream.ktx.com

> After connecting, the client can send the following JSON format request to the server

```json
{
"id": 123, // Request ID given by the client
"method": "Subscribe", // Request type
"params":["spot.BTC_USDT.order_book.5"]
}
```

> After receiving the request, the server will send the following json format response to the client

```json
{
  "result": "success", // Back results
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
"data": ..., // data
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
> Symbol is the name of the transaction pair, such as BTC_USDT, ETH_USDT, etc.
> Data_type is a data type, and currently only supports the following data types
> Order_Book: Deep
> trades: trading list
> Candles: K line
> TICKER: The latest transaction information

> After data_type is the parameter list, different data types have different parameter lists, these will be introduced in the following context

> Request: Cancel the subscription data stream

```javascript
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

```javascript
{
  "result": "success", // Back results
  "op":"SUBSCRIBE",
}
```

> If the request is wrong, the client will receive the following error response:

```javascript
{
"error": -1003, // Error code
"message": "..." // Error description
}
```

## Request Methods

> Request type and parameter

> The client can send the following requests to the server

```javascript
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
| UnSubscripe | 1. Cancel the subscription data stream <br/> 2. The parameter is the list of data stream names <br/> 3. After successfully canceling the subscription, the client will no longer receive the corresponding data flow |

## Subscribe Order Book

**Subscribe in-depth information**

> Send the following request to subscribe to the in-depth information

```javascript
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, etc.
3. \<max_depth> is the maximum depth, the effective value is 5, 10, 20, 50, 100, 200, 500, 1000

> Data flow

```javascript
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

```javascript
  ...
```

## Subscribe Trades

**Subscribe to transaction list**

> Send the following request to subscribe to the transaction list

```javascript
{
  "id": 123,
  "method": "SUBSCRIBE"
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, etc.

> Data flow

```javascript
{
"stream": "spot.BTC_USDT.trades",
"data":  {
    "i": 17122255, // Transaction ID
      "p": "46125.7", // The transaction price
      "q": "0.079045", // Transaction volume
      "s": "1", // Taker's transaction direction 1 represents buy -1 representative sells
      "t": "1628738748319" // Transaction time
  },
  {
    "i": 17122254, // Transaction ID
    "p": "46125", // The transaction price
    "q": "0.079047", // Transaction volume
    "s": "1", // Taker's transaction direction 1 represents buy -1 representative sells
    "t": "1628738748318" // Transaction time
  }
...
}
```

## Subscribe K-line

**Subscribe to K-line**

> Send the following request to subscribe to the K-line

```javascript
{
  "id": 123,
  "method": "SUBSCRIBE"
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, etc.
3. \<time_frame> is the K-line period, the effective value is 1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 6h, 12h, 1d, 1w, 1M
> Data flow

```javascript
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

```javascript
{
  "id": 123,
  "method": "SUBSCRIBE"
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, etc.
> Data flow

```javascript
{
"stream": "spot.BTC_USDT.ticker",
  "data": {
    "askPrice": "98100", // Sell for one price
    "product": "BTC_USDT", // Transaction pair
    "amount": "922635", // 24 transaction value
    "last": "98000", // Latest transaction price
    "firstTradeId": 1, // The first transaction id
    "change": "0", // Price changes
    "bidQty": "1.7", // Sell a quantity
    "bidPrice": "98000", // Buy one price
    "volume": "9.41", // 24h transaction quantity
    "lastQty": "0.3", // 24h latest deal
    "askQty": "0.5", // Sell a quantity
    "high": "98100", // The highest price of 24
    "tradeCount": 30, // Number of transactions
    "low": "98000", // 24h lowest price
    "time": "1733474204000", // Time
    "Open": "98000" // Opening price
    }
}
```

---

# User Data Endpoints



## Get Trade Account Asset

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/accounts?${queryStr}`;

request.get(url,{
          headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
          },
        },

        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('upload failed:', err);
          }
          console.log(body) // 7.the result

        });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/accounts'
    query_str = 'asset=BTC'
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time': expire_time
    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
  "asset":"USDT", // Asset code
  "balance":10000, // Total amount
  "holds":0, // Freeze amount
  "withdrawable":0,// Transferable
  "collateral":false,// Collateral
  },
  {
  "asset": "USDT", // Asset code
  "balance":10000, // Total amount
  "holds":0, // Freeze amount
  "withdrawable":0,// Transferable
  "collateral":false,// Collateral
  },
  ...
]
```

**Get the balance, freeze and other information of various assets in the corresponding account by API Key**

* Request method get
* Request path /v1 /accounts
* Permissions: View, Trade
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
| ---------- | ---------- | ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------|
| asset | string | No | Asset code, such as BTC, ETH, etc.<br/> Multiple asset codes can be specified in the following two forms<br/>1. /v1/accounts?asset=BTC,ETH<br/> 2. /v1/accounts?asset=BTC&asset=ETH <br/> If the asset parameter is not specified, the information of all assets will be returned |

* Data Source

Cache


## Get Main Account Asset

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    asset:'USDT'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/tf/main/assets`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import json
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'asset': 'USDT'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/tf/main/assets'
    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.post(END_POINT + path, json=param, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "state": "0",
  "msg": null,
  "result": [
    {
      "asset": "USDT",//Asset code
      "balance": "2081.0000000000",//Total Amount
      "holds": "498.0000000000"//Freeze Amount
    }
  ]
}
```

**Get Main Account Assets**

* Request method POST
* Request path /v1/tf/main/assets
* Permissions: Trade
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
| ---------- | ---------- | ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------|
| asset | string | No | Asset code, such as BTC, ETH, etc.<br/> If the asset parameter is not specified, the information of all assets will be returned |


## Asset Transfer

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'USDT',
    amount: 10
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/tf/transfer`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import json
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'symbol': 'USDT',
        'amount': 10
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/tf/transfer'
    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.post(END_POINT + path, json=param, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "state": "0",
  "msg": null,
  "result": {}
}
```

**Asset Tranfer**

* Request method POST
* Request path /v1/tf/transfer
* Permissions: Trade
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                               |
| ---------- |----------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| symbol | string  | Yes | Asset code, such as BTC, ETH                                                                                                                              |
| amount | number  | Yes | Transfer Amount, such as 10, -10, <br/> If > 0, transfer from main account to trade account <br/> If < 0, transfer from trade account to main account |


## Get an account's ledger

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC&end_time=1651895799668&limit=10';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/ledger?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/ledger'
    query_str = 'asset=BTC&end_time=1651895799668&limit=10'
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time': expire_time

    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
  "amount": "10000", // The number of changes
  "balance": "10000", // balance
  "id": "1125899906842624029", // ID
  "time": "1733468814795", // time
  "asset": "USDT", // Asset code
  "type": "Transfer" // Bill type
  }
  ...
]
```

**Obtain the bill of accounts for the API Key account, including all records that change the balance of the account, such as capital transfer, transaction, handling fees, etc.**

* Request method get
* Request path /v1 /ledger
* Permanent: View, Trade
* Request parameters (need sorting)


| Parameter name | Parameter type | Whether to pass it? | Description |
|----------------| ---------- | ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------|
| asset          | string | No | Asset code, such as BTC, ETH, etc.<br/> Multiple asset codes can be specified in the following two forms<br/>1. /v1/ledger?asset=BTC,ETH<br/> 2./V1/LEDger? ASSET = BTC & Asset = ETH <br/> If the ASSET parameters are not specified, return the bill record of all assets |
| start_time     | int64 | No | The earliest time of limited returning bill records |
| end_time       | int64 | No | Limited to return the latest time of the billing record |
| before         | int64 | No | Bill record id<br/>Limit the maximum id value of the return bill record |
| after          | int64 | No | Bill record id<br/>Limit the minimum id value of return bill record |
| limit          | int32 | No | Limited to return the maximum number of bill records<br/>Default value 100 |
| type           | String | No | Bill type transfer transfer, trade transaction, fee handling fee, refite system charged, funding funds fee |

* Data Source

DB

## Create an order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'BTC_USDT',
    quantity:'0.0001',
    price:'90000',
    type:'limit',
    market:'spot',
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/order`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });

```

```python
import hashlib
import hmac
import requests
import json
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():

    param = {
      'symbol':'BTC_USDT',
      'quantity':'0.0001',
      'price':'90000',
      'type':'limit',
      'market':'spot',
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/order'
    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.post(END_POINT + path, json=param, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "orderId": "4611767382287843330", // Order id
  "clientOrderId": "", // Custom ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT", // Transaction to the code to the code
  "type": "Limit", // Order type
  "side": "Buy", // Trading direction
  "quantity": "0.01", // quantity
  "stf": "disabled",
  "price": "10300", // The commission price
  "visibleQty": "0.01",
  "timeInforce": "GTC",
  "cancelAfter": 0,
  "postOnly": false,
  "positionMerge": "None", // position mode None divide the position Long merged multi
  "positionId": 0, // Submitted position id
  "close": false, // Is it a flat order
  "leverage": 0, // Leverage multiple
  "action": "unknown", // position behavior
  "status": "Filled", // Order status
  "executedQty": "0.01", //
  "Profit": "0", // return
  "executedCost": "103", // The transaction value has
  "fillCount": 1, // Number of transactions
  "fills": [// transaction details
    {
      "tradeId": 1,
      "time": "1733390650379",
      "price": "10300",
      "quantity": "0.01",
      "profit": "0",
      "taker": false,
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

```

**Submit the entrustment**

* Request method POST
* Request path /v1/order
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|-----------------|---------|------|-----------------------------------------------------------------------------------------------------------------------|
| symbol | string | Yes | Transaction pair codes, such as BTC_USDT, ETH_USDT, etc. |
| type | string | Yes | Delegate type, valid value limit market |
| Quantity | DECIMAL | Yes | The commission is positive and negative |
| market | string | Yes | Must spot spot, lpc U-standard perpetual |
| client_order_id | string | No | Delegate id, a string with a valid value of int64 integer, it is recommended to use the Unix timestamp when submitting the delegate |
| Price | DECIMAL | No | Entrusted price limit |
| POSITIONMERGE | String | No | Contract must be to merge multi -short merged empty |
| marginMethod | string | No | Contract must be isolate position by position, cross full position |
| leverage | int | No | Contract must be leverage multiple
| close | bool | No | The contract must be true to close the warehouse receipt, false to open the warehouse receipt |
| post_only | bool | No | ... |
| Time_in_Force | String | No | Effective time performance <br/> Effective value GTC, IOC <br/> GTC indicates that the commission that has not been fully transaction will always be effective until the user revokes the commission <br/> IOC indicating that the matching will be immediately revoked to the bottom below The commission that cannot be completely sold at all times, <br/> Any transaction will be retained <br/> default value GTC |
| POSITIONID | String | No | Warehouse ID |

> Delegate object
> It contains up to 20 transactions entrusted
> If there are more than 20 transactions in the delegation, then the object only contains the last 20 transactions. Please obtain other transactions through the fills interface.

## Get an order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'id=4611772879845982339';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/order?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/order'
    query_str = 'id=14118828812271651'
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "orderId": "4611767382287843330", // Order id
  "clientOrderId": "", // Custom ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT", // Transaction to the code to the code
  "type": "Limit", // Order type
  "side": "Buy", // Trading direction
  "quantity": "0.01", // quantity
  "stf": "disabled",
  "price": "10300", // The commission price
  "visibleQty": "0.01",
  "timeInforce": "GTC",
  "cancelAfter": 0,
  "postOnly": false,
  "positionMerge": "None", // position mode None divide the position Long merged multi
  "positionId": 0, // Submitted position id
  "close": false, // Is it a flat order
  "leverage": 0, // Leverage multiple
  "action": "unknown", // position behavior
  "status": "Filled", // Order status
  "executedQty": "0.01", //
  "Profit": "0", // return
  "executedCost": "103", // The transaction value has
  "fillCount": 1, // Number of transactions
  "fills": [// transaction details
    {
      "tradeId": 1,
      "time": "1733390650379",
      "price": "10300",
      "quantity": "0.01",
      "profit": "0",
      "taker": false,
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
```

**Get the commission of the specified ID**

* Request method get
* Request path /v1/order
* Permissions: View, Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
| ---------- | ---------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID | String | Yes | Entrusted ID <br/> The entrustment ID can be allocated by the exchange, <br/> can also be customized by users (using the client_order_id parameter when submitting the commission). When defining IDs, you need to add "C:" prefix before ID. <br/> For example: using a custom ID "123" when submitting commission, when obtaining the commission, you need to use "C: 123".

## Get Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'limit=2&status=settled&market=spot&symbol=BTC_USDT';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/orders?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/orders'
    query_str = 'limit=2&status=settled&market=spot&symbol=BTC_USDT'
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "orderId": "4611767382287843330", // Order id
    "clientOrderId": "", // Custom ID
    "createTime": "1733390630904", // Creation time
    "product": "BTC_USDT", // Transaction to the code to the code
    "type": "Limit", // Order type
    "side": "Buy", // Trading direction
    "quantity": "0.01", // quantity
    "stf": "disabled",
    "price": "10300", // The commission price
    "visibleQty": "0.01",
    "timeInforce": "GTC",
    "cancelAfter": 0,
    "postOnly": false,
    "positionMerge": "None", // position mode None divide the position Long merged multi
    "positionId": 0, // Submitted position id
    "close": false, // Is it a flat order
    "leverage": 0, // Leverage multiple
    "action": "unknown", // position behavior
    "status": "Filled", // Order status
    "executedQty": "0.01", //
    "Profit": "0", // return
    "executedCost": "103", // The transaction value has
    "fillCount": 1, // Number of transactions
    "fills": [// transaction details
      {
        "tradeId": 1,
        "time": "1733390650379",
        "price": "10300",
        "quantity": "0.01",
        "profit": "0",
        "taker": false,
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
  ...
]
```

**Obtain the delegation in the corresponding ApiKey account that meets the following conditions**

1. All unsettled commissions
2. The settlement commission of the settlement within three months, including rejection, revoked and transaction commission
3. All have been commissioned
4. All trading commissions that have been revoked

* Request method get
* Request path /v1 /order
* Permanent: View, Trade
* Request parameters (need sorting)


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                                                                             |
|----------------| ---------- |----|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| status         | String | No | Valid value unsettedled, settled <br/> Unsettedled indicates that the uncomfortable commission is obtained, the return result is sorted by the entrusted creation time. <br/> default value unsettedled |
| market         | String | No | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract<br/> default value spot                                                                                        |
| Symbol         | String | No | Trading code, such as BTC_USDT, ETH_USDT, etc. <br/> When status = unsettled, Symbol will return to all the uncomfortable commissioned entrustment of all transaction pairs <br/> Symbol parameter      |
| start_time     | long | No | Limited return to the last creation time of the delegation                                                                                                                                              |
| end_time       | long | No | Limited return to the last creation time of the delegation                                                                                                                                              |
| beFore         | int64 | No | Entrust update ID <br/> Limited to return to the maximum update ID                                                                                                                                      |
| after          | int64 | No | Entrust update ID <br/> Limited to the minimum update ID of the entrustment                                                                                                                             |
| limit          | Long | No | How many commissioneds are the specified?                                                                                                                                                               

* Parameter combinations and data sources supported by this interface

* status=unsettled + symbol
* status=settled + symbol + start_time
* status=settled + symbol + start_time + limit
* status=settled + symbol + end_time
* status=settled + symbol + end_time + limit
* status=settled + symbol + start_time + end_time
* status=settled + symbol + start_time + end_time + limit
* status=settled + symbol + before
* status=settled + symbol + before + limit
* status=settled + symbol + after
* status=settled + symbol + after + limit

> The returned unsettled delegation is sorted from early to near by creation time
> The returned setled delegation is sorted from early to near according to settlement time

## Cancel an Order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    id:'14244173146202090'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/order/delete`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import json
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'id': '14245272657638034'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/order/delete'
    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.post(END_POINT + path, json=param, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[n] // Cancel quantity
```

**Revoke the delegation of the specified id**

* Request method POST
* Request path /v1/order/delete
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|----------------| ---------- |-----| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id | String | Yes | Entrusted ID <br> The entrusted ID can be allocated by the exchange, <br/> It can also be customized by the user (using the client_order_id parameter when submitting the commission). <br>When using a custom id, you need to add the "c:" prefix before the id. <br/>For example: the custom id "123" is used when submitting the delegation, and when revoking the delegation, "c:123" is required. | |
| market | String | Yes | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract |

> If the delegate with the specified id has been settled, or if the delegate with the specified id does not exist, you will receive an error -30001.

## Cancel all Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'BTC_USDT'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/orders/delete`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import json
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'symbol': 'BTC_USDT'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/orders/delete'
    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 

    }
    resp = requests.post(END_POINT + path, json=param, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[n] // Cancel quantity
```

**Rejected all commissioned commissioned**

* Request method POST
* Request path /v1 /orders/delete
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|--------| ---------- |-----|---------------------------------------|
| market | string | Yes | trading pair markets, such as spot, lpc, etc., spot is spot, lpc is U-standard contract |
| symbol | string | Yes | Transaction pair code<br/>such as BTC_USDT, ETH_USDT, etc. |
| Side | String | No | Buy or Sell |

> If the request is executed correctly, return an empty array, otherwise return an error message

## Get fills

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/api'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'limit=2&market=spot&symbol=BTC_USDT';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/fills?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apikey,
            'api-sign': sign,
            'api-expire-time':exprieTime 

        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('upload failed:', err);
        }
        console.log(body) // 7.the result

    });
```

```python
import hashlib
import hmac
import requests
import time

END_POINT = 'https://api.ktx.com/api'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/fills'
    query_str = 'limit=2&market=spot&symbol=BTC_USDT'
    # POST or DELETE replace query_str with body_str
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + query_str).encode("utf-8"), hashlib.sha256).hexdigest()

    headers = {
        'Content-Type': 'application/json',
        'api-key': API_KEY,
        'api-sign': sign,
        'api-expire-time':expire_time 
    }
    resp = requests.get(END_POINT + path, query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
  "product":"BTC_USDT_SWAP", // Transaction pair code
  "fees": [{"amount": "10", "asset": "usdt", "value": "10"}], // fees
  "quantity": "0.01", // The number of transactions
  "orderId":"4611772879845982371", // Order id
  "price":"1000000", // Transaction price
  "time":"1733541360859", // Transaction time
  "taker":true, // Is it a order
  "profit":"-9060", // Revenue
  "tradeId": 26
  },
...
]
```

**Get transaction records**

* Request method get
* Request path /v1/fills
* Permanent: View, Trade
* Request parameters (need sorting)


| Parameter name | Parameter type | Whether to pass it? | Description |
|----------------| ---------- | ---------- | ------------------------------------------------------------------------------------------------------------------ |
| market         | String | Yes | Trading to the market, such as spot, LPC, etc., spot is spot, LPC is a U -based contract |
| order_id       | string | No | Delegation id assigned by the exchange<br/>Limit only return transaction records for the specified delegation<br/>If this parameter is not specified, please specify symbol |
| symbol         | string | No | Transaction pair code<br/>For example, BTC_USDT, ETH_USDT, etc.<br/>Limit only return transaction records for the specified transaction pair<br/>If this parameter is not specified, please specify order_id |
| start_time     | int64 | No | The earliest time of the return transaction records |
| end_time       | int64 | No | Limited to return the latest time of transaction record |
| BeFore         | int64 | No | Transaction record ID <br/> Limited to return the maximum ID of the transaction record |
| after          | int64 | No | Transaction record id<br/>Limit the minimum id to return transaction record |
| limit          | int32 | No | Limited to the maximum number of returned results<br/>Default value 100 |

* The parameter combination and data source supported by the interface

* symbol  --> database
* symbol + limit  --> database
* symbol + start_time  --> database
* symbol + start_time + limit  --> database
* symbol + end_time  --> database
* symbol + end_time + limit  --> database
* symbol + start_time + end_time  --> database
* symbol + start_time + end_time + limit  --> database
* symbol + before  --> database
* symbol + before + limit  --> database
* symbol + after  --> database
* symbol + after + limit  --> database
* order_id  --> database
* order_id + limit  --> database
* order_id + before  --> database
* order_id + before + limit  --> database

> Return results sorted from small to large by transaction record id

# User Data Streams

## Overview

> Example

```javascript
const CryptoJS = require("crypto-js");
const WebSocket = require('ws');
const madexws = 'wss://u-stream.ktx.com';
const apikey = "9e2bd17ff73e8531c0f3c26f93e48bfa402a3b13"; // your apikey
const secret = "ca55beb9e45d4f30b3959b464402319b9e12bac7"; // your secret
const sign = CryptoJS.HmacSHA256("/user/verify", secret).toString();

let wsClass = function () {
};


wsClass.prototype._initWs = async function () {
  let that = this;
  console.log(madexws);

  let ws = new WebSocket(madexws);
  that.ws = ws;

  ws.on('open', function open() {
    console.log(new Date(), 'open')
    ws.send(JSON.stringify({
      "method": "LOGIN",
      "auth": {
        "api-key": apikey, "api-sign": sign,
      }
    }));
    setInterval(function () {
      ws.ping(Date.now())
    },30000)
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
API_KEY = '9e2bd17ff73e8531c0f3c26f93e48bfa402a3b13'
SECRET_KEY = 'ca55beb9e45d4f30b3959b464402319b9e12bac7'
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
    time.sleep(3)

    data = {
      "ping": datetime.now().timestamp() * 1000
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

Use Websocket push service to obtain account balance and delegation changes information in a timely manner.

**Connect to WebSocket server**

Please use the following URL to connect to the Websocket server:

wss://u-stream.ktx.com

**Please attach the following HTTP request header when connecting**

* api-key
* API-SIGN
* api-expire-time

*For specific methods, please refer to the [Authentication](#authentication) chapter*

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
  "data": {
  "asset":"USDT", // Asset code
  "balance":"100000", // Balance
  "Holds": "20016.9970000" // Frozen
}
```

## Position

**When the position information is sent to change, you will receive the Position event**
```json
{
  "stream": "position",
  "data": {
  "id": "1125899906842624003", // position ID
  "symbol": "BTC_USDT_SWAP", // Transaction to the code to the code
  "quantity": "0", // quantity
  "entryPrice":"0", // Average price for opening positions
  "mergeMode": "None", // position mode
  "marginMethod":"isolate",//Position mode
  "leverage":"10.0", // bar
  "initMargin":"0.1", // Start margin rate
  "maintMargin": "0.005", // Maintain the margin rate
  "posMargin": "0", // Press margin
  "orderMargin":"1009.8990000" // Entrustment deposit
}
```
## Order

**When the delegation changes, the order event will be received**

```json
{
"stream": "order",
"data":{
    "orderId": "4611767382287843330", // Order id
      "clientOrderId": "", // Custom ID
      "createTime": "1733390630904", // Creation time
      "Product": "BTC_USDT", // Transaction to the code to the code
      "type": "Limit", // Order type
      "side": "Buy", // Trading direction
      "quantity": "0.01", // quantity
      "stf": "disabled",
      "price": "10300", // The commission price
      "visibleQty": "0.01",
      "timeInforce": "GTC",
      "cancelAfter": 0,
      "postOnly": false,
      "positionMerge": "None", // position mode None divide the position Long merged multi
      "positionId": 0, // Submitted position id
      "close": false, // Is it a flat order
      "leverage": 0, // Leverage multiple
      "action": "unknown", // position behavior
      "status": "Filled", // Order status
      "executedQty": "0.01", //
      "Profit": "0", // return
      "executedCost": "103", // The transaction value has
      "fillCount": 1, // Number of transactions
      "fills": [// transaction details
      {
        "tradeId": 1,
        "time": "1733390650379",
        "price": "10300",
        "quantity": "0.01",
        "profit": "0",
        "taker": false,
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
```
