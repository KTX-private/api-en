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
* User Data Endpoints: https://api.ktx.com/papi (the old version url is end with /api, should use /papi)
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

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString(); // POST or DELETE  replace queryStr with bodyStr
const url = `${endpoints}/v1/trade/accounts?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/trade/accounts'
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

## Ping Test

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/ping`
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
    path = '/v1/ping'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
{}
```



## Get Server Time

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/time`
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

END_POINT = 'https://api.ktx.com/time';

def do_request():
    path = '/v1/ping'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
{"time":"1746777864508"}
```


## Get Coins

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/coins`
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
    path = '/v1/coins'
    resp = requests.get(END_POINT + path)
    print(resp.text)
  
if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "asset": "USDT", // asset name
    "valid_decimals": 8, // asset decimals
    "enable_transfer": 1, // Allow transfer [0: No | 1: Yes]
    "chains": [
      {
        "coin_symbol": "USDT", // Coin symbol
        "chain_type": "Tron (TRC20)", // network
        "enable_withdraw": 1, // Allow withdraw [0: No | 1: Yes]
        "enable_deposit": 1, // Allow deposit [0: No | 1: Yes]
        "original_decimals": 6 // decimal
      },
      {
        "coin_symbol": "eUSDT",
        "chain_type": "Ethereum (ERC20)",
        "enable_withdraw": 1,
        "enable_deposit": 1,
        "original_decimals": 6
      },
      {
        "coin_symbol": "bUSDT",
        "chain_type": "BNB Smart Chain (BEP20)",
        "enable_withdraw": 1,
        "enable_deposit": 1,
        "original_decimals": 18
      },
      {
        "coin_symbol": "sUSDT",
        "chain_type": "Solana",
        "enable_withdraw": 1,
        "enable_deposit": 1,
        "original_decimals": 6
      }
    ]
  } 
]
```

**Get coins**

* Request method get
* Request path /v1/coins
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
| ---------- | ---------- | ---------- |-----------------------------------------------------------------------------------------------------|

## Get products

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
    "ID": 5,// ID
    "market": "lpc", // Transaction pair market [spot: Spot | lpc: USDT-margined perpetual]
    "Symbol": "BTC_USDT_SWAP", // Transaction pair
    "takerFee": "0.001", // Taker fee rate
    "Makerfee": "0.001", // Maker fee rate
    "minOrderSize": "0.0001", // Minimum order quantity
    "maxOrderSize": "10000000", // Maximum order quantity
    "quantityScale": 4, // Quantity scale
    "priceScale": 4, // Price scale
    "minOrderValue": "0.0001", // Minimum order value
    "maxOrderValue": "1000000000" // Maximum order value
  }
]
```

**Get the product list**

* Request method get
* Request path /v1/products
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                      |
|--------|----------------|------|--------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| Symbol | string         | No | Trading pair code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                              |

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
"i": 1027024, // Update ID
"t": "1644558642100", // update time
"b": [// Bids
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ],,,
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ],,,
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ]
  ...
  ],
"a": [// Asks
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ],,,
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ],,,
  [
  "46125.7", // Order price
  "0.079045" // Order quantity
  ]
  ...
  ]
}
```

**Get in-depth data**

* Request method get
* Request path /v1/order_book
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol         | string         | Yes | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| level          | int32          | No | How many level depth is specified? <br/> Effective value 1, 2, 5, 10, 20, 50, 100, 500, 1000 <br/> The default value 100 |
| price_scale    | Integer        | No | Price precision merge [0: 4 decimals; 1: 3 decimals; 2: 2 decimals; 3: 1 decimal; 4: 0 decimals]. Default value: 0 |

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
* Request path /v1/candles
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                         |
|----------------|----------------| ---------- |---------------------------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol         | string         | Yes | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                                                        |
| time_frame     | string         | Yes | Kline period [1m, 3m, 5m, 15m, 30m, 1h, 2h, 4h, 6h, 12h, 1d, 1W, 1M] |
| before         | int64          | No | utc time<br/>Limit the latest time of return to the K-line record                                                   |
| after          | int64          | No | UTC Time <br/> Limited to return the earliest time of the K -line records                                           |
| limit          | Integer        | No | Get the maximum number of K -line records <br/> The default value is 100, the maximum value is 1000                 |

* The parameter combination and data source supported by the interface

1. market + symbol + time_frame  --> cache
2. market + symbol + time_frame + limit  --> cache
3. market + symbol + time_frame + before  --> database
4. market + symbol + time_frame + before + limit  --> database
5. market + symbol + time_frame + after  --> database
6. market + symbol + time_frame + after + limit  --> database

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
  "s": 1, // Taker direction [1: Buy | -1: Sell]
  "t": "1628738748319" // Transaction time
  },
  ...
]
```

**Get the transaction record**

* Request method get
* Request path /v1/trades
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                       |
|----------------| ---------- |---------------------------|---------------------------------------------------------------------------------------------------|
| market | string | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol         | string | Yes                       | Trading pair codes, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                            |
| start_time     | int64 | No                        | The earliest time of limited returning transaction records                                        |
| end_time       | int64 | No                        | Limited recent time of returning transaction records                                              |
| before         | int64 | No                        | Transaction record ID<br/> Limited to return the maximum id of the transaction record             |
| after          | int64 | No                        | Trading record ID <br/>Transaction record ID, limit the minimum ID of returning transaction records |
| limit          | Integer | No                        | The maximum number of obtaining records <br/> The default value is 100, the maximum value is 1000 |

* Parameter combinations and data sources supported by this interface

1. market + symbol  --> cache
2. market + symbol + limit  --> cache
3. market + symbol + start_time  --> database
4. market + symbol + start_time + limit  --> database
5. market + symbol + end_time  --> database
6. market + symbol + end_time + limit  --> database
7. market + symbol + start_time + end_time  --> database
8. market + symbol + start_time + end_time + limit  --> database
9. market + symbol + before  --> database
10. market + symbol + before + limit  --> database
11. market + symbol + after  --> database
12. market + symbol + after + limit  --> database

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
* Request path /v1/ticker
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                                          |
|----------------|----------------| ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol         | string         | Yes | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc. <br/> You can specify multiple transactions in the following two forms <br/> 1.symbol=BTC_USDT,ETH_USDT |

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
"data": ..., // Data
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
> Symbol is the name of the transaction pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
> Data_type is a data type, and currently only supports the following data types
> Order_Book: Deep
> trades: trading list
> Candles: K line
> TICKER: The latest transaction information

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
  "op":"SUBSCRIBE",
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
| UnSubscripe | 1. Cancel the subscription data stream <br/> 2. The parameter is the list of data stream names <br/> 3. After successfully canceling the subscription, the client will no longer receive the corresponding data flow |

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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.

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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
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
2. \<symbol> is the name of the transaction pair, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.
> Data flow

```json
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

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/trade/accounts?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/trade/accounts'
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
    "asset":"USDT",  // Asset code
    "balance":"100",  // Total amount
    "locked":"0",  // Freeze amount
    "free":"100",  //  Available Amount
    "withdrawable":"100",// Transferable
    "collateral":false,// Is collateral [true: Yes | false: No]
    "discountForMargin":"1", // Margin discount rate [0: Unavailable | 0.5: 50% | 1: 100%]
    "discountForFee":"1" // Fee discount rate [0: Unavailable | 0.5: 50% | 1: 100%]
  },
  {
    "asset":"BTC",  // Asset code
    "balance":"100",  // Total amount
    "locked":"0",  // Freeze amount
    "free":"100",  //  Available Amount
    "withdrawable":"100",// Transferable
    "collateral":false,// Is collateral [true: Yes | false: No]
  },
  ...
]
```
},
...
]
```

**Get the balance, freeze and other information of various assets in the corresponding account by API Key**

* Request method get
* Request path /v1/trade/accounts
* Permissions: View
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
| ---------- | ---------- | ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------|
| asset | string | No | Asset code, such as BTC, ETH, etc.<br/> Multiple asset codes can be specified in the following two forms<br/>1. accounts?asset=BTC,ETH<br/> 2. accounts?asset=BTC&asset=ETH <br/> If the asset parameter is not specified, the information of all assets will be returned |

* Data Source

Cache

## Get Addr

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    coin_symbol:'sUSDT'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/depositAddr`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'coin_symbol': 'sUSDT'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/depositAddr'
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
[
  {
    "addr": "74VZm4a6eGGBW3VCq6umcPL8QPhs5fFnpcT9nyQGgHkw", // Address
    "coin_id": "100", // Coin ID 
    "coin_symbol": "sUSDT",// Coin symbol
    "chain_type":"Solana", // network
    "general_name": "USDT" ,// asset name
    "mx_uid": "de4a8cdc-6be5-3253-9f34-d2c61a89286f"
  }
]
```

**Get Addr**

* Request method post
* Request path /v1/depositAddr
* Permissions: View
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
|-------------| ---------- |------|---------------------------------------------|
| coin_symbol | string | No    | coin_symbol from /v1/coins ,like USDT,sUSDT,BTC ... |

## Withdraw

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    coin_symbol:"BTC",
    amount:"0.001",
    addr:"bc1qksfjx5ezznnngk6grt04h8lwnta2mxtmjl0etm"
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/withdraw`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'coin_symbol':'BTC',
        'amount':'0.001',
        'addr':'bc1qksfjx5ezznnngk6grt04h8lwnta2mxtmjl0etm'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/withdraw'
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
[
  {
    "id": 624, // Withdraw ID
    "to_address": "bc1qksfjx5ezznnngk6grt04h8lwnta2mxtmjl0etm", // to address
    "amount_real": "9.00000000", // arrive amount
    "amount": "10.00000000",  // amount
    "fee": "1.00000000",  // fee
    "chain_type": "Bitcoin", // network
    "coin_symbol": 'BTC' // Coin symbol
  }
]
```

**Withdraw**

* Request method post
* Request path /v1/withdraw
* Permissions: Withdraw
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
|----------------|--------|---------------------|-----------------------------------------------------|
| coin_symbol    | string | No                  | coin_symbol from /v1/coins ,like USDT,sUSDT,BTC ... |
| addr           | string | No                  | to addr                                             |
| amount         | number | No                  | amount                                              |
| memo           | string | No                  | memo                                                |
| withdraw_id    | string | No                  | User defined ID                                                                                                                                        |

## Get Main Account Asset

> Request


```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/main/accounts?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/main/accounts'
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
    "asset": "USDT", //Asset code
    "balance": "100", // Total Amount
    "locked": "0",// Freeze Amount
    "free": "100"// Available Amount
  }
]

```

**Get Main Account Assets**

* Request method GET
* Request path /v1/main/accounts
* Permissions: View
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description |
| ---------- | ---------- | ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------|
| asset | string | No | Asset code, such as BTC, ETH, etc.<br/> If the asset parameter is not specified, the information of all assets will be returned |


## Asset Transfer

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'USDT',
    amount: 10,
    type:'WALLET_TRADE'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/transfer`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'symbol': 'USDT',
        'amount': 10,
        'type':'WALLET_TRADE',
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/transfer'
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
  "asset": "BTC",
  "balance": "1.123"
}
```

**Asset Tranfer**

* Request method POST
* Request path /v1/transfer
* Permissions: Trade
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                           |
|----------------|----------------|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| symbol         | string         | Yes                 | Asset code, such as BTC, ETH                                                                                                                          |
| amount         | number         | Yes                 | Transfer Amount, such as 10, -10, <br/> If > 0, transfer from main account to trade account <br/> If < 0, transfer from trade account to main account |
| type           | string         | Yes                 | If type is WALLET_TRADE ,transfer from main account to trade account <br/> If type is TRADE_WALLET , transfer from trade account to main account      |
| transfer_id | string | No                  | User defined ID                                                                                                                                        |

## Sub Account Asset Transfer

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
  'symbol':'BTC',
  'amount':'0.001',
  'sub_user_id':30000416,
  'side':'in',
  'transfer_id':'userdefineid001',
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/subaccount/transfer`;

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
        'symbol':'BTC',
        'amount':'0.001',
        'sub_user_id':30000416,
        'side':'in',
        'transfer_id':'userdefineid001',
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/transfer'
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
  "asset": "BTC",
  "balance": "1.123"
}
```

**Sub account Asset Tranfer**

* Request method POST
* Request path /v1/subaccount/transfer
* Permissions: Trade
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                           |
|----------------|----------------|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| symbol         | string         | Yes                 | Asset code, such as BTC, ETH                                                                                                                          |
| amount         | number         | Yes                 | Transfer Amount, such as 10, -10, <br/> If > 0, transfer from main account to trade account <br/> If < 0, transfer from trade account to main account |
| sub_user_id      | number | Yes                 | Sub account ID                                                                                                                                        |
| side        | string | Yes                 | in mean transfer in sub account  and out mean transfer from sub account                                                                               |
| transfer_id | string | No                  | User defined ID                                                                                                                                        |

## Get an account's ledger

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'asset=BTC&end_time=1651895799668&limit=10';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/ledgers?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/ledgers'
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
  "time": "1733468814795", // Time (ms)
  "asset": "USDT", // Asset code
  "type": "Transfer" // Bill type
  }
  ...
]
```

**Obtain the bill of accounts for the API Key account, including all records that change the balance of the account, such as capital transfer, transaction, handling fees, etc.**

* Request method get
* Request path /v1/ledgers
* Permanent: View


| Parameter name | Parameter type | Whether to pass it? | Description |
|----------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------|
| asset          | string         | No | Asset code, such as BTC, ETH, etc.<br/> Multiple asset codes can be specified in the following two forms<br/>1. /v1/ledger?asset=BTC,ETH<br/> 2./V1/LEDger? ASSET = BTC & Asset = ETH <br/> If the ASSET parameters are not specified, return the bill record of all assets |
| start_time     | int64          | No | The earliest time of limited returning bill records |
| end_time       | int64          | No | Limited to return the latest time of the billing record |
| before         | int64          | No | Bill record id<br/>Limit the maximum id value of the return bill record |
| after          | int64          | No | Bill record id<br/>Limit the minimum id value of return bill record |
| limit          | int32          | No | Limited to return the maximum number of bill records<br/>Default value 100 |
| type           | string         | No | Bill type [transfer: transfer; trade: trade; fee: fee; rebate: rebate; funding: funding fee] |

* Data Source

DB

## Create an order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'BTC_USDT',
    side:'buy',
    quantity:'0.0001',
    price:'90000',
    type:'limit',
    market:'spot',
}

/* 
mini trade 
open long example
const param = {
  symbol:'BTC_USDT_SWAP',
  side:'buy',
  quantity:'0.0001',
  type:'market',
  market:'lpc',
  leverage:20,
  mini:true,
  positionMerge:'none',
  marginMethod:'isolate'
}
close long example
const param = {
  symbol:'BTC_USDT_SWAP',
  side:'sell',
  quantity:'0.0001',
  type:'market',
  market:'lpc',
  leverage:20,
  mini:true,
  positionMerge:'none',
  marginMethod:'isolate'
  positionId:1125899906842649789
  close:true
}


close position with take profit or stop loss
stop loss one long position 
const param = {
  symbol:'BTC_USDT_SWAP',
  side:'sell',
  quantity:'0.0001',
  type:'stop', // stop loss with market price,if you want to trigger with limit price you can set this val: stop-limit,and also set price param to define your limit price
  trigger_price:'50000', // the price < last price
  market:'lpc',
  leverage:20,
  positionMerge:'long',
  marginMethod:'cross'
  positionId:1125899906842649789
  close:true
}
take profit one long position 
const param = {
  symbol:'BTC_USDT_SWAP',
  side:'sell',
  quantity:'0.0001',
  type:'take-profit', // take profit with market price,if you want to trigger with limit price you can set this val: take-profit-limit,and also set price param to define your limit price
  trigger_price:'100000', // the price > last price
  market:'lpc',
  leverage:20,
  positionMerge:'long',
  marginMethod:'cross'
  positionId:1125899906842649789
  close:true
}

open position with take profit or stop loss

open long
const param = {
  symbol:'BTC_USDT_SWAP',
  side:'buy',
  quantity:'0.0001',
  price:'73816.6',
  type:'limit', 
  market:'lpc',
  leverage:20,
  positionMerge:'long',
  marginMethod:'cross'
  close:false,
  tpo_trigger:1, // enable take profit
  tpo_trigger_value:'80000', // the price > last price
  slo_trigger:1, // enable stop loss
  slo_trigger_value:'50000', // the price < last price
}

空仓止盈止损 trigger_price,tpo_trigger_value,slo_trigger_value的处理和多仓相反

*/

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():

    param = {
      'symbol':'BTC_USDT',
      'side':'buy',
      'quantity':'0.0001',
      'price':'90000',
      'type':'limit',
      'market':'spot',
    }
    """
    mini trade 
    open long example
    param = {
      'symbol':'BTC_USDT_SWAP',
      'side':'buy',
      'quantity':'0.0001',
      'type':'market',
      'market':'lpc',
      'leverage':20,
      'mini':true
      'positionMerge':'none',
      'marginMethod':'isolate'
    }
    close long example
    param = {
      'symbol':'BTC_USDT_SWAP',
      'side':'sell',
      'quantity':'0.0001',
      'type':'market',
      'market':'lpc',
      'leverage':20,
      'mini':true
      'positionMerge':'none',
      'marginMethod':'isolate'
      'positionId':1125899906842649789
      'close':true
    }
    """
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
  "orderId": "4611767382287843330", // Order ID
  "clientOrderId": "", // Client Order ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT", // Transaction pair
  "type": "Limit", // Order type [limit: Limit | market: Market | take-profit: Take profit | stop: Stop | take-profit-limit: Take profit limit | stop-limit: Stop limit]
  "side": "Buy", // Trading direction [buy: Buy | sell: Sell]
  "quantity": "0.01", // Quantity
  "stf": "disabled", // Self-trading prevention [0: disabled Disable self-trade prevention | 1: dc Decrease and Cancel | 2: co Cancel Oldest | 3: cn Cancel Newest | 4: cb Cancel Both]
  "price": "10300", // The commission price
  "timeInforce": "gtc", // Time in force [gtc: Good till cancel | ioc: Immediate or cancel | fok: Fill or kill]
  "mini":"false", // Is mini contract [true: Yes | false: No]
  "cancelAfter": 0, // Cancel after N seconds [>0: Cancel after N seconds | 0: Never auto-cancel]
  "postOnly": false, // Post only [true: Yes | false: No]
  "positionMerge": "long", // Position merge mode [long: Merge long | short: Merge short | none: Split]
  "positionId": 0, // Submitted position id
  "close": false, // Is close position [true: Close | false: Open]
  "leverage": 0, // Leverage multiple
  "action": "unknown", // Position behavior [unknown: Unknown | increase_long: Open Long | reduce_long: Close Long | increase_short: Open Short | reduce_short: Close Short]
  "status": "accepted", // Order status [accepted: Accepted | partial-filled: Partial Filled | filled: Filled | cancelled: Cancelled | rejected: Rejected | partially-cancelled: Partially Cancelled]
  "executedQty": "0", // executed quantity
  "profit": "0", // return
  "origin":0, // Origin [when origin=-1, this order is a liquidation order]
  "brokerId":0, // broker id
  "update_id":'1125899907137993336', // update id
  "executedCost": "0", // The transaction value has
  "fillCount": 1, // Number of transactions
  "fills": [],// transaction details
  "fees": [],// handle fee
  "updateTime": "1733390650379" // Update time
}

```

**Submit the entrustment**

* Request method POST
* Request path /v1/order
* Permissions: Trade
* Request parameters


| Parameter Name  | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| symbol          | string         | Yes                      | Trading pair codes, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                                                                                                                                                                                                                                                                                                                                                     |
| side            | string         | Yes                      | buy or sell                                                                                                                                                                                                                                                                                                                                                                                                                |
| type            | string         | Yes                      | Delegate type, valid value limit or market or take-profit or stop or take-profit-limit(need define price param after trigger) or stop-limit (need define price param after trigger)                                                                                                                                                                                                                                             |
| quantity        | DECIMAL        | Yes                      | Delegate quantity                                                                                                                                                                                                                                                                                                                                                                                                          |
| market          | string         | Yes                      | Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| client_order_id | string         | No                       | Delegate ID, a string with a valid value of int64 integer, it is recommended to use the Unix timestamp when submitting the delegate                                                                                                                                                                                                                                                                                        |
| price           | DECIMAL        | No                       | Entrusted price limit                                                                                                                                                                                                                                                                                                                                                                                                      |
| positionMerge   | string         | No                       | long or short exp:open long(positionMerge=long,side=buy),close long(positionMerge=long,side=sell),open short(positionMerge=short,side=sell),close short(positionMerge=short,side=buy)                                                                                                                                                                                                                                      |
| marginMethod    | string         | No                       | Contract must be isolate position by position, cross full position                                                                                                                                                                                                                                                                                                                                                         |
| mini            | bool           | No                       | mini trade ,if is true , must be positionMerge=none&&marginMethod=isolate&&type=limit                                                                                                                                                                                                                                                                                                                                      |
| leverage        | int            | No                       | Leverage                                                                                                                                                                                                                                                                                                                                                                                         |
| close           | bool           | No                       | The contract must be true to close the warehouse receipt, false to open the warehouse receipt                                                                                                                                                                                                                                                                                                                              |
| post_only       | bool           | No                       | Post only                                                                                                                                                                                                                                                                                                                                                                                                                  |
| time_in_Force   | string         | No                       | Effective time performance <br/> Effective value GTC, IOC,FOK <br/> GTC indicates that the commission that has not been fully transaction will always be effective until the user revokes the commission <br/> IOC indicating that the matching will be immediately revoked to the bottom below The commission that cannot be completely sold at all times, <br/> Any transaction will be retained <br/> default value GTC |
| positionId      | string         | No                       | Position ID                                                                                                                                                                                                                                                                                                                                                                                                               |
| trigger_price         | decimal | No       | TP/SL trigger price                                                                                                                                                                                                                                                                                                                                                                              |
| tpo_trigger         | int     | No                         | open position with take profit,need use with tpo_trigger_value    0 disabled 1 enable                                                                                                                                                                                                                                                                                                                                      |
| slo_trigger         | int     | No                        | open position with stop loss,need use with slo_trigger_value    0 disabled 1 enable                                                                                                                                                                                                                                                                                                                                        |
| tpo_trigger_value         | decimal | No                        | open position  take profit price                                                                                                                                                                                                                                                                                                                                                                                           |
| slo_trigger_value         | decimal | No                        | open position  stop loss price                                                                                                                                                                                                                                                                                                                                                                                             |

> Delegate object
> It contains up to 20 transactions entrusted
> If there are more than 20 transactions in the delegation, then the object only contains the last 20 transactions. Please obtain other transactions through the fills interface.

## Get an order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
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

END_POINT = 'https://api.ktx.com/papi'
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
  "orderId": "4611767382287843330", // Order ID
  "clientOrderId": "", // Client Order ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT_SWAP", // Transaction pair code
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
      "side":"buy", // Trading direction [buy: Buy | sell: Sell]
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
* Permissions: View
* Request parameters

| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id            | string         | Yes | Entrusted ID <br/> The entrustment ID can be allocated by the exchange, <br/> can also be customized by users (using the client_order_id parameter when submitting the commission). When defining IDs, you need to add "C:" prefix before ID. <br/> For example: using a custom ID "123" when submitting commission, when obtaining the commission, you need to use "C: 123".

## Get History Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'limit=2&market=spot&symbol=BTC_USDT';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/history/orders?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/history/orders'
    query_str = 'limit=2&market=spot&symbol=BTC_USDT'
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
    "orderId": "4611767382287843330", // Order ID
    "clientOrderId": "", // Client Order ID
    "createTime": "1733390630904", // Creation time
    "product": "BTC_USDT_SWAP", // Transaction pair code
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
        "side":"buy", // Trading direction [buy: Buy | sell: Sell]
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

2. The settlement commission of the settlement within three months, including rejection, revoked and transaction commission
3. All have been commissioned
4. All trading commissions that have been revoked

* Request method get
* Request path /v1/history/order
* Permanent: View, Trade


| Parameter name | Parameter type | Whether to pass it? | Description                                                  |
| -------------- | -------------- | ------------------- | ------------------------------------------------------------ |
| market         | string         | Yes                 | Trading pair market [spot: spot; lpc: USDT-M perpetual]      |
| Symbol         | string         | No                  | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc. <br/> When status = unsettled, Symbol will return to all the uncomfortable commissioned entrustment of all transaction pairs <br/> Symbol parameter |
| start_time     | long           | No                  | Limit the earliest creation time of returned orders          |
| end_time       | long           | No                  | Limit the latest creation time of returned orders            |
| before         | int64          | No                  | Order update ID, limit the maximum update ID of returned orders |
| after          | int64          | No                  | Entrust update ID <br/> Limited to the minimum update ID of the entrustment |
| limit          | long           | No                  | How many commissioneds are the specified?                    |

* Parameter combinations and data sources supported by this interface

  *  market + symbol + start_time
  *  market + symbol + start_time + limit
  *  market + symbol + end_time
  *  market + symbol + end_time + limit
  *  market + symbol + start_time + end_time
  *  market + symbol + start_time + end_time + limit
  *  market + symbol + before
  *  market + symbol + before + limit
  *  market + symbol + after
  *  market + symbol + after + limit

> The returned settled delegation is sorted from early to near according to settlement time

## Get Pending Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'market=spot&symbol=BTC_USDT';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/pending/orders?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/pending/orders'
    query_str = 'market=spot&symbol=BTC_USDT'
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
    "orderId": "4611767382287843330", // Order ID
    "clientOrderId": "", // Client Order ID
    "createTime": "1733390630904", // Creation time
    "product": "BTC_USDT_SWAP", // Transaction pair code
  "type": "limit", // Order type [limit: Limit | market: Market | take-profit: Take profit | stop: Stop | take-profit-limit: Take profit limit | stop-limit: Stop limit]
  "side": "buy", // Trading direction [buy: Buy | sell: Sell]
  "quantity": "0.01", // Quantity
  "stf": "disabled", // Self-trading prevention [0: disabled Disable self-trade prevention | 1: dc Decrease and Cancel | 2: co Cancel Oldest | 3: cn Cancel Newest | 4: cb Cancel Both]
  "price": "10300", // The commission price
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
  "status": "accepted", // Order status [accepted: Accepted | partial-filled: Partial Filled | filled: Filled | cancelled: Cancelled | rejected: Rejected | partially-cancelled: Partially Cancelled]
    "executedQty": "0", // executed quantity
    "profit": "0", // return
    "origin":0, // Origin [when origin=-1, this order is a liquidation order]
    "brokerId":0, // broker id
    "update_id":'1125899907137993336', // update id
    "executedCost": "0", // The transaction value has
    "fillCount": 1, // Number of transactions
    "fills": [],// transaction details
    "fees": [],// handle fee
    "updateTime": "1733390650379" // Update time
  },
  ...
]
```

**Get Pending Orders**

* Request method get
* Request path /v1/pending/order
* Permanent: View, Trade


| Parameter name | Parameter type | Whether to pass it? | Description                                                  |
| -------------- | -------------- | ------------------- | ------------------------------------------------------------ |
| market         | string         | Yes                 | Trading pair market [spot: spot; lpc: USDT-M perpetual]      |
| Symbol         | string         | No                  | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc. <br/> When status = unsettled, Symbol will return to all the uncomfortable commissioned entrustment of all transaction pairs <br/> Symbol parameter |


> The returned unsettled delegation is sorted from early to near by creation time

## Set Position leverage

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
  positionId: '1125899906842649789',
  leverage:20,
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/change/leverage`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'positionId': '1125899906842649789',
        'leverage': 20
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/change/leverage'
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
   
```

**Set Position leverage**

* Request method POST
* Request path /v1/change/leverage
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description |
|------------|--------|------|-----------------|
| positionId | string | yes  | position id     |
| leverage   | int    | yes  | leverage number |


## Adjust Margin

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
  positionId: '1125899906842649789',
  amount: 12,
  type: 1,
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/margin/transfer`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'positionId': '1125899906842649789',
        'amount': 12,
        'type': 1,
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/margin/transfer'
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
   
```

**Adjust Margin**

* Request method POST
* Request path /v1/margin/transfer
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description    |
|------------|--------|---------------------------|----------------|
| positionId | string | yes                       | position id    |
| type       | int    | yes                       | 1 add 2 reduce |
| amount     | decimal | yes                         | amount         |


## Cancel an Order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
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

END_POINT = 'https://api.ktx.com/papi'
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
|----------------|----------------|-----| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| id | string         | Yes | Entrusted ID <br> The entrusted ID can be allocated by the exchange, <br/> It can also be customized by the user (using the client_order_id parameter when submitting the commission). <br>When using a custom id, you need to add the "c:" prefix before the id. <br/>For example: the custom id "123" is used when submitting the delegation, and when revoking the delegation, "c:123" is required. |
| market | string         | Yes | Trading pair market [spot: spot; lpc: USDT-M perpetual] |


## Cancel all Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    symbol:'BTC_USDT',
    market:'spot'
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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'symbol': 'BTC_USDT',
        'market': 'spot'

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
* Request path /v1/orders/delete
* Permissions: Trade
* Request parameters


| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                             |
|--------|----------------|-----|-----------------------------------------------------------------------------------------|
| market | string         | Yes | trading pair markets, such as spot, lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol | string         | Yes | Trading pair code<br/>such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                |
| Side | string         | No | Buy or Sell                                                                             |

> If the request is executed correctly, return an empty array, otherwise return an error message

## Get positions

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'market=lpc&symbol=BTC_USDT_SWAP';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/positions?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/positions'
    query_str = 'market=lpc&symbol=BTC_USDT_SWAP'
    expire_time = str(int(time.time() * 1000) + 5000)
    # POST or DELETE replace query_str with body_str
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
    "entryPrice": "109398.9", // Entry price
    "symbol": "BTC_USDT_SWAP", // Transaction pair
    "leverage": "10.0", // Leverage
    "maintMargin": "0.0050000000", // Maintenance margin ratio
    "side": "short", // Position direction [long: Long | short: Short]
    "quantity": "0.100", // Position quantity
    "posMargin": "1093.989", // Position margin
    "marginMethod": "cross", // Margin mode [isolate: Isolated | cross: Cross]
    "closableQty": "0.100", // Closable quantity
    "initMargin": "0.1000000000", // Initial margin rate
    "id": "1125899906842624158", // Position ID
    "orderMargin": "0",  // Order margin
    "mergeMode": "short"  // Position merge mode [long: Merge long | short: Merge short]
  }
  ...
]
```

**Get positions**


* Request method get
* Request path /v1/positions
* Permanent: View
* Request parameters

| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| position_id   | string   | No                        | Position ID, the main param                                                                 |
| market | string   | No                        | trading pair markets, such as spot, lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol     | string   | No                        | use symbol with market param ,transaction pair code<br/>such as BTC_USDT_SWAP, ETH_USDT_SWAP |

### Response Parameters Enum Values

#### side (Position Side)

| Value | Description |
|----|------|
| long | Long position, bullish position |
| short | Short position, bearish position |

#### marginMethod (Margin Mode)

| Value | Description |
|----|------|
| isolate | Isolated margin, each position has independent margin |
| cross | Cross margin, all positions share margin pool |

#### mergeMode (Position Merge Mode)

| Value | Description |
|----|------|
| long | Merge to long, multiple same-direction long positions merged |
| short | Merge to short, multiple same-direction short positions merged |

## Get fills

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
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

END_POINT = 'https://api.ktx.com/papi'
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
  "side":"buy", // Trading direction [buy: Buy | sell: Sell]
  "fees": [{"amount": "10", "asset": "usdt", "value": "10"}], // fees
  "quantity": "0.01", // The number of transactions
  "orderId":"4611772879845982371", // Order ID
  "fillId":"4611471874845582393", // Fill id
  "price":"1000000", // Trade price
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


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                                                                                                |
|----------------|----------------| ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | string         | Yes | Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| order_id       | string         | No | Delegation ID assigned by the exchange<br/>Limit only return transaction records for the specified delegation<br/>If this parameter is not specified, please specify symbol                                                |
| symbol         | string         | No | Trading pair code<br/>For example, BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.<br/>Limit only return transaction records for the specified trading pair<br/>If this parameter is not specified, please specify order_id |
| start_time     | int64          | No | The earliest time of the return transaction records                                                                                                                                                                        |
| end_time       | int64          | No | Limited to return the latest time of transaction record                                                                                                                                                                    |
| beFore         | int64          | No | fillId <br/> Limited to return the maximum ID of the transaction record                                                                                                                                                    |
| after          | int64          | No | fillId<br/>Limit the minimum id to return transaction record                                                                                                                                                |
| limit          | int32          | No | Limited to the maximum number of returned results<br/>Default value 100                                                                                                                                                    |

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

### Response Parameters Enum Values

#### taker (Is Taker)

| Value | Description |
|----|------|
| true | Taker, active party who takes liquidity |
| false | Maker, passive party who provides liquidity |

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
  "symbol": "BTC_USDT_SWAP", // Transaction pair code
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
      "Product": "BTC_USDT_SWAP", // Transaction pair code
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

# FORECAST
## Get Event

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/forecast/events`;
request.get(url, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('request failed:', err);
  }

  console.log(body);
});
```

```python
import requests

END_POINT = 'https://api.ktx.com/api'


def do_request():
    path = '/v1/forecast/events'
    resp = requests.get(END_POINT + path)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "endDate": "1781204400000", // End time
    "description": "This event is for the upcoming FIFA World Cup game, scheduled for Thursday, June 11, 2026 between Mexico and South Africa.", // Description
    "id": "351715", // ID
    "title": "Mexico vs. South Africa", // Title
    "slug": "fifwc-mex-rsa-2026-06-11", // Slug
    "startDate": "1775515727000", // Start time
    "status": "active" // Market status [active: active | completed: completed]
  }
  ...
]
```

**Get events**

* Request Method: `GET`
* Request Path: `/v1/forecast/events`

---

## Get Events Details

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/forecast/event/detail`;
request.get(url, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('request failed:', err);
  }

  console.log(body);
});
```

```python
import requests

END_POINT = 'https://api.ktx.com/api'


def do_request():
    path = '/v1/forecast/event/detail'
    resp = requests.get(END_POINT + path)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "endDate": "1781204400000", // End time
    "description": "This event is for the upcoming FIFA World Cup game, scheduled for Thursday, June 11, 2026 between Mexico and South Africa.", // Description
    "id": "351715", // ID
    "title": "Mexico vs. South Africa", // Title
    "slug": "fifwc-mex-rsa-2026-06-11", // Slug
    "startDate": "1775515727000", // Start time
    "status": "active" // Market status [active: active | completed: completed]
    "markets": [ // May contain multiple markets
      {
        "eventId": "351715", // Event ID
        "symbol": "1897034_FORECAST", // Trading symbol
        "question": "Will Mexico win on 2026-06-11?", // Market question
        "takerFee": "0", // Taker fee rate
        "endDate": "1781204400000", // End time
        "outcomes": [ // Available answer options
          "Yes", // Corresponds to the long order parameter
          "No"   // Corresponds to the short order parameter
        ],
        "winningOutcome": 0, // [0: unresolved | 1: yes (long) wins | -1: no (short) wins | 2: draw]
        "makerFee": "0.00040000", // Maker fee rate
        "quantityScale": 0, // Quantity precision
        "priceScale": 2, // Price precision
        "id": "1897034", // Market ID
        "slug": "fifwc-mex-rsa-2026-06-11-mex", // Slug
        "startDate": "1775515619000", // Start time
        "status": "active" // Market status [active: active | resolved: settled]
      }
      ...
    ],
  }
  ...
]
```

**Get events details**

* Request Method: `GET`
* Request Path: `/v1/forecast/event/detail`
* Request parameters

| Parameter Name | Parameter Type | Required | Description            |
|------| ---------- |------|-------------|
| id             | string         | Yes      | The specified event ID |

## Get market

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/forecast/markets`;
request.get(url, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('request failed:', err);
  }

  console.log(body);
});
```

```python
import requests

END_POINT = 'https://api.ktx.com/api'


def do_request():
    path = '/v1/forecast/markets'
    resp = requests.get(END_POINT + path)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
[
  {
    "id": "1897034", // Market ID
    "eventId": "351715", // Associated event ID
    "slug": "fifwc-mex-rsa-2026-06-11-mex", // Unique market identifier
    "symbol": "1897034_FORECAST", // Trading symbol
    "question": "Will Mexico win on 2026-06-11?", // Prediction question [Format: Will {event subject} {outcome} on {date}?]
    "outcomes": [ // Prediction outcome options
      "Yes", // Yes / Win [corresponds to the long order parameter]
      "No"   // No / Lose [corresponds to the short order parameter]
    ],
    "winningOutcome": 0, // Settlement result [0: unresolved | 1: Yes wins | -1: No wins | 2: draw]
    "status": "active", // Market status [active: active | resolved: settled]
    "startDate": "1775515619000", // Trading start time [millisecond timestamp]
    "endDate": "1781204400000", // End / settlement time [millisecond timestamp]
    "takerFee": "0", // Taker fee rate [fee charged to liquidity takers]
    "makerFee": "0.00040000", // Maker fee rate [fee charged to liquidity providers]
    "quantityScale": 0, // Quantity precision [number of decimal places; 0 means integers only]
    "priceScale": 2 // Price precision [number of decimal places; 2 means keep 2 decimal places]
  }
]
```

**Get market**

* Request Method: `GET`
* Request Path: `/v1/forecast/markets`
* Request parameters


| Parameter Name | Type | Required | Description |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| eventId  | string   | No       | Specific event ID |
| symbol   | string   | No       | Prediction market trading symbol in the format `{marketId}_FORECAST`, such as `1897034_FORECAST` or `2362124_FORECAST` |

---

## Get ticker

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/ticker/get_all?market=forecast&forecastSide=yes`;
request.get(url, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('request failed:', err);
  }

  console.log(body);
});
```

```python
import requests

END_POINT = 'https://api.ktx.com/api'


def do_request():
    path = '/v1/ticker/get_all?market=forecast&forecastSide=yes'
    resp = requests.get(END_POINT + path)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "state": 0, // Response status [0: success | non-zero: failure]
  "result": [
    {
      "productId": 759, // Trading pair ID
      "product": "2434164_FORECAST", // Trading symbol 
      "time": "1780644690000", // Timestamp [milliseconds]
      "last": "0.2235", // Last traded price [prediction market price, range 0-1, representing the probability of the event occurring]
      "lastQty": "15.1", // Most recent trade quantity [number of contracts]
      "bidPrice": "0.2234", // Best bid price [highest buy price, representing the buyer's view of the event probability]
      "bidQty": "4.7", // Best bid quantity [number of contracts on the best bid]
      "askPrice": "0.2237", // Best ask price [lowest sell price, representing the seller's view of the event probability]
      "askQty": "4.7", // Best ask quantity [number of contracts on the best ask]
      "open": "0.2435", // 24h opening price [price 24 hours ago]
      "high": "0.2440", // 24h highest price [highest probability]
      "low": "0.2227", // 24h lowest price [lowest probability]
      "change": "-0.0818", // 24h price change [positive: increase | negative: decrease, representing the absolute change in implied probability]
      "volume": "112068.8", // 24h trading volume [number of contracts]
      "amount": "26181.59142", // 24h traded amount [quote currency amount, such as USDT]
      "tradeCount": 9496, // 24h trade count [number of matched trades]
      "firstTradeId": 194285 // First trade ID
    }
  ]
}
```

**Get ticker**

* Request Method: `GET`
* Request Path: `/v1/ticker/get_all`
* Request Parameters

| Parameter Name | Type | Required | Description |
| -------- | -------- | -------- | ------------------------------------------------------------ |
| market   | string   | Yes      | Trading market [forecast: prediction market] |
| forecastSide  | string  | No      | Order book side (yes: yes direction; no: no direction). Default value: yes                                                                                                                               |

> Note: The returned ticker is default displayed from the `Yes` (`long`) perspective if without forecastSide.
---

## Get Order Book

> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/order_book?market=forecast&symbol=2362124_FORECAST&forecastSide=no`;
request.get(url, function optionalCallback(err, httpResponse, body) {
  if (err) {
    return console.error('request failed:', err);
  }

  console.log(body);
});
```

```python
import requests

END_POINT = 'https://api.ktx.com/api'


def do_request():
    path = '/v1/order_book?market=forecast&symbol=2362124_FORECAST&forecastSide=no'
    resp = requests.get(END_POINT + path)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

> Response

```json
{
  "i": 1027024, // Update ID [order book version number, incremented on each change]
  "t": "1644558642100", // Update time [update timestamp, milliseconds]
  "b": [ // Bid book [buy-side order queue, sorted from highest price to lowest price, representing the probability price buyers are willing to buy at]
    [
      "0.65", // Order price [price buyers are willing to pay, implying a 65% probability of the event occurring]
      "100.5" // Order quantity [number of contracts to buy]
    ],
    [
      "0.64", // Order price [price buyers are willing to pay, implying a 64% probability of the event occurring]
      "200.3" // Order quantity [number of contracts to buy]
    ],
    [
      "0.63", // Order price [price buyers are willing to pay, implying a 63% probability of the event occurring]
      "150.2" // Order quantity [number of contracts to buy]
    ]
  ],
  "a": [ // Ask book [sell-side order queue, sorted from lowest price to highest price, representing the probability price sellers are willing to sell at]
    [
      "0.66", // Order price [price sellers are willing to accept, implying a 66% probability of the event occurring]
      "80.4" // Order quantity [number of contracts to sell]
    ],
    [
      "0.67", // Order price [price sellers are willing to accept, implying a 67% probability of the event occurring]
      "120.6" // Order quantity [number of contracts to sell]
    ],
    [
      "0.68", // Order price [price sellers are willing to accept, implying a 68% probability of the event occurring]
      "90.1" // Order quantity [number of contracts to sell]
    ]
  ]
}
```

**Get order book**

* Request Method: `GET`
* Request Path: `/v1/order_book`
* Request Parameters

| Parameter Name | Type | Required | Description                                                                                                                                                                                              |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market        | string  | No      | Trading market [forecast: prediction market]                                                                                                                                                             |
| symbol        | string  | Yes     | Prediction market trading symbol in the format `{marketId}_FORECAST`, such as `1897034_FORECAST` or `2362124_FORECAST`                                                                                   |
| level         | int32   | No      | Maximum returned depth level. For batch queries, you can use `symbol=1897034_FORECAST,1897035_FORECAST`. Valid values: `1`, `2`, `5`, `10`, `20`, `50`, `100`, `200`, `500`, `1000`. Default value: `100` |
| price_scale   | integer | No      | Aggregated price precision. 0=4 decimal places, 1=3 decimal places, 2=2 decimal places, 3=1 decimal place, 4=0 decimal places. Default value: 0                                                          |
| forecastSide  | string  | No      | Order book side (yes: yes direction; no: no direction). Default value: yes                                                                                                                               |

> Note: The returned order book is default displayed from the `Yes` (`long`) perspective  if without forecastSide.


## Create Prediction Market Order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

// 80% rate price buy yes win
const param = {
  market:'forecast',
  symbol:'2362124_FORECAST',
  forecastSide:'buy_long', // buy yes
  quantity:'1',
  price:'0.8', // 80% rate price
  type:'limit',
}

/*
// 80% rate price buy no win
const param = {
    market:'forecast',
    symbol:'2362124_FORECAST',
    forecastSide:'buy_short', // buy no
    quantity:'1',
    price:'0.8', // 80% rate price
    type:'limit',
}
 */



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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():

    param = {
      'market':'forecast',
      'symbol':'2362124_FORECAST',
      'forecastSide':'buy_long',
      'quantity':'1',
      'price':'0.8',
      'type':'limit',
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
  "update_id": "1125900141912261326", // Update ID
  "fees": [
    {
      "amount": "0.0354", // Fee amount
      "asset": "USDT", // Fee asset code
      "value": "0" // Fee valuation (USDT)
    }
  ],
  "executedQty": "3", // Executed quantity (number of contracts)
  "orderId": "4620329279333336924", // Order ID
  "origin": 1, // Order origin
  "type": "limit", // Order type [limit: limit order; market: market order]
  "executedCost": "1.77", // Executed cost (USDT)
  "forecastSide": "buy_long", // Prediction direction [buy_long: buy yes; buy_short: buy no; sell_long: sell yes; sell_short: sell no]
  "price": "0.59", // Order price (probability, range 0-1)
  "timeInForce": "fok", // Time in force [gtc: Good till cancel; ioc: Immediate or cancel; fok: Fill or kill]
  "introduction": "Bitcoin Up or Down - June 6, 3AM ET", // Event description
  "fills": [
    {
      "fees": [
        {
          "amount": "0.0354", // Fee amount
          "asset": "USDT", // Fee asset code
          "value": "0" // Fee valuation (USDT)
        }
      ],
      "quantity": "3", // Filled quantity (number of contracts)
      "price": "0.59", // Filled price (probability, range 0-1)
      "time": "1780732741150", // Fill time (millisecond timestamp)
      "taker": true, // Is Taker [true: yes; false: no]
      "profit": "0", // Profit (positive: profit, negative: loss)
      "tradeId": 203 // Trade ID
    }
  ],
  "brokerId": 0, // Broker/channel ID
  "product": "2434164_FORECAST", // Trading pair code, format {marketId}_FORECAST
  "quantity": "3", // Order quantity (number of contracts)
  "stf": "disabled", // Self-trade prevention [disabled: disable; dc: decrease and cancel; co: cancel oldest; cn: cancel newest; cb: cancel both]
  "clientOrderId": "", // Custom order ID
  "cancelAfter": 0, // Auto-cancel after N seconds [>0: cancel after N seconds; 0: never auto-cancel]
  "updateTime": "1780732741150", // Last update time (millisecond timestamp)
  "postOnly": false, // Post only [true: yes; false: no]
  "market": "forecast", // Market type [forecast: prediction market]
  "createTime": "1780732741150", // Creation time (millisecond timestamp)
  "visibleQty": "-1", // Visible quantity
  "fillCount": 1, // Fill count
  "status": "filled" // Order status [accepted: accepted; partial-filled: partially filled; filled: filled; cancelled: cancelled; rejected: rejected; partially-cancelled: partially cancelled]
}
```

**Create Prediction Market Order**

* Request Method: POST

* Request Path: /v1/order

* Permissions: Trade

* Request Parameters

| Parameter Name | Parameter Type | Required | Description                                                                                          |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | string        | Yes     | Market type [forecast: prediction market]                                                            |
| symbol         | string        | Yes     | Trading pair code, e.g., `2362124_FORECAST`                                                          |
| type           | string        | Yes     | Order type [limit: limit order; market: market order]                                                |
| quantity       | decimal       | Yes     | Order quantity (number of contracts)                                                                 |
| forecastSide   | string        | Yes     | Prediction direction [buy_long: buy yes; buy_short: buy no; sell_long: sell yes; sell_short: sell no] |
| client_order_id | string        | No      | Custom order ID, valid as int64 string, recommended to use Unix timestamp                            |
| price          | decimal       | No      | Order price (probability, range 0-1, mean win rate, required for limit orders)                       |
| time_in_force  | string        | No      | Time in force [gtc: Good till cancel; ioc: Immediate or cancel; fok: Fill or kill]. Default: `gtc`   |


## Get Order Details

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
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

END_POINT = 'https://api.ktx.com/papi'
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
  "update_id": "1125900141912261326", // Update ID
  "fees": [
    {
      "amount": "0.0354", // Amount
      "asset": "USDT", // Asset code
      "value": "0" // Valuation
    }
  ],
  "executedQty": "3", // Executed quantity
  "orderId": "4620329279333336924", // Order ID
  "origin": 1, // Order origin
  "type": "limit", // Order type [limit: limit order | market: market order]
  "executedCost": "1.77", // Executed cost (USDT)
  "forecastSide": "buy_long", // Prediction direction [buy_long: buy yes; buy_short: buy no; sell_long: sell yes; sell_short: sell no]
  "price": "0.59", // Order price (probability, range 0-1)
  "timeInForce": "fok", // Time in force [gtc: Good till cancel | ioc: Immediate or cancel | fok: Fill or kill]
  "introduction": "Bitcoin Up or Down - June 6, 3AM ET", // Event description
  "fills": [
    {
      "fees": [
        {
          "amount": "0.0354", // Amount
          "asset": "USDT", // Asset code
          "value": "0" // Valuation
        }
      ],
      "quantity": "3", // Filled quantity (number of contracts)
      "price": "0.59", // Filled price (probability, range 0-1)
      "time": "1780732741150", // Fill time
      "taker": true, // Is Taker [true: yes | false: no]
      "profit": "0", // Profit
      "tradeId": 203 // Trade ID
    }
  ],
  "brokerId": 0, // Broker ID
  "product": "2434164_FORECAST", // Trading pair code, format {marketId}_FORECAST
  "quantity": "3", // Order quantity (number of contracts)
  "stf": "disabled", // Self-trade prevention [disabled: disable | dc: decrease and cancel | co: cancel oldest | cn: cancel newest | cb: cancel both]
  "clientOrderId": "", // Custom order ID
  "cancelAfter": 0, // Auto cancel after N seconds [>0: cancel after N seconds | 0: never auto cancel]
  "updateTime": "1780732741150", // Update time
  "postOnly": false, // Post only [true: yes | false: no]
  "market": "forecast", // Market type [forecast: prediction market]
  "createTime": "1780732741150", // Creation time
  "visibleQty": "-1", // Visible quantity
  "fillCount": 1, // Fill count
  "status": "filled" // Order status [accepted: accepted | partial-filled: partially filled | filled: filled | cancelled: cancelled | rejected: rejected | partially-cancelled: partially cancelled]
}
```

**Get an Order by ID**

* Request Method: GET
* Request Path: /v1/order
* Permissions: View
* Request Parameters


| Parameter Name | Parameter Type | Required | Description                                                  |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | string         | No       | Trading pair market [forecast: prediction market]            |
| id             | string         | Yes      | Order ID The order ID can be assigned by the exchange, or customized by the user (using the client_order_id parameter when submitting the order). When using a custom ID, you need to add the "c:" prefix before the ID. For example: if you used custom ID "123" when submitting the order, use "c:123" when querying the order. |

## Get Order History

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'limit=2&market=forecast&symbol=2434164_FORECAST';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/history/orders?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/history/orders'
    query_str = 'limit=2&market=forecast&symbol=2434164_FORECAST'
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
    "update_id": "1125900141912261326", // Update ID
    "fees": [
      {
        "amount": "0.0354", // Amount
        "asset": "USDT", // Asset code
        "value": "0" // Valuation
      }
    ],
    "executedQty": "3", // Executed quantity
    "orderId": "4620329279333336924", // Order ID
    "origin": 1, // Order origin
    "type": "limit", // Order type [limit: limit order | market: market order]
    "executedCost": "1.77", // Executed cost (USDT)
    "forecastSide": "buy_long", // Prediction direction [buy_long: buy yes; buy_short: buy no; sell_long: sell yes; sell_short: sell no]
    "price": "0.59", // Order price (probability, range 0-1)
    "timeInForce": "fok", // Time in force [gtc: Good till cancel | ioc: Immediate or cancel | fok: Fill or kill]
    "introduction": "Bitcoin Up or Down - June 6, 3AM ET", // Event description
    "fills": [
      {
        "fees": [
          {
            "amount": "0.0354", // Amount
            "asset": "USDT", // Asset code
            "value": "0" // Valuation
          }
        ],
        "quantity": "3", // Filled quantity (number of contracts)
        "price": "0.59", // Filled price (probability, range 0-1)
        "time": "1780732741150", // Fill time
        "taker": true, // Is Taker [true: yes | false: no]
        "profit": "0", // Profit
        "tradeId": 203 // Trade ID
      }
    ],
    "brokerId": 0, // Broker ID
    "product": "2434164_FORECAST", // Trading pair code, format {marketId}_FORECAST
    "quantity": "3", // Order quantity (number of contracts)
    "stf": "disabled", // Self-trade prevention [disabled: disable | dc: decrease and cancel | co: cancel oldest | cn: cancel newest | cb: cancel both]
    "clientOrderId": "", // Custom order ID
    "cancelAfter": 0, // Auto cancel after N seconds [>0: cancel after N seconds | 0: never auto cancel]
    "updateTime": "1780732741150", // Update time
    "postOnly": false, // Post only [true: yes | false: no]
    "market": "forecast", // Market type [forecast: prediction market]
    "createTime": "1780732741150", // Creation time
    "visibleQty": "-1", // Visible quantity
    "fillCount": 1, // Fill count
    "status": "filled" // Order status [accepted: accepted | partial-filled: partially filled | filled: filled | cancelled: cancelled | rejected: rejected | partially-cancelled: partially cancelled]
  }
  ...
]
```

**Get Order History**
1、Settled orders within three months, including rejected, cancelled, and filled orders
2、All filled orders
3、All partially filled and then cancelled orders

* Request Method: GET

* Request Path: /v1/history/orders

* Permissions: View

* Request Parameters

| Parameter Name | Parameter Type | Required | Description                                                 |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market        | string        | No      | Trading pair market [forecast: prediction market]           |
| symbol        | string        | No      | Trading pair code, e.g., `2362124_FORECAST` When `status=unsettled`, not specifying symbol will return all unsettled orders of all trading pairs When `status=settled`, the symbol parameter is required |
| start_time    | long          | No      | Limit the earliest creation time of returned orders         |
| end_time      | long          | No      | Limit the latest creation time of returned orders           |
| before        | int64         | No      | Order update ID, limit the maximum update ID of returned orders |
| after         | int64         | No      | Order update ID, limit the minimum update ID of returned orders |
| limit         | long          | No      | Specify the maximum number of orders to return              |

## Get Open Orders

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'market=forecast&symbol=2434164_FORECAST';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/pending/orders?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():
    path = '/v1/pending/orders'
    query_str = 'market=forecast&symbol=2434164_FORECAST'
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
    "update_id": "1125900141912261326", // Update ID
    "fees": [],
    "executedQty": "0", // Executed quantity
    "orderId": "4620329279333336924", // Order ID
    "origin": 1, // Order origin
    "type": "limit", // Order type [limit: limit order | market: market order]
    "executedCost": "0", // Executed cost (USDT)
    "forecastSide": "buy_long", // Prediction direction [buy_long: buy yes; buy_short: buy no; sell_long: sell yes; sell_short: sell no]
    "price": "0.59", // Order price (probability, range 0-1)
    "timeInForce": "fok", // Time in force [gtc: Good till cancel | ioc: Immediate or cancel | fok: Fill or kill]
    "introduction": "Bitcoin Up or Down - June 6, 3AM ET", // Event description
    "fills": [],
    "brokerId": 0, // Broker ID
    "product": "2434164_FORECAST", // Trading pair code, format {marketId}_FORECAST
    "quantity": "3", // Order quantity (number of contracts)
    "stf": "disabled", // Self-trade prevention [disabled: disable | dc: decrease and cancel | co: cancel oldest | cn: cancel newest | cb: cancel both]
    "clientOrderId": "", // Custom order ID
    "cancelAfter": 0, // Auto cancel after N seconds [>0: cancel after N seconds | 0: never auto cancel]
    "updateTime": "1780732741150", // Update time
    "postOnly": false, // Post only [true: yes | false: no]
    "market": "forecast", // Market type [forecast: prediction market]
    "createTime": "1780732741150", // Creation time
    "visibleQty": "-1", // Visible quantity
    "fillCount": 1, // Fill count
    "status": "accepted" // Order status [accepted: accepted | partial-filled: partially filled | filled: filled | cancelled: cancelled | rejected: rejected | partially-cancelled: partially cancelled]
  }
  ...
]
```

**Get Open Orders**

* Request Method: GET

* Request Path: /v1/pending/orders

* Permissions: View

* Request Parameters

| Parameter Name | Parameter Type | Required | Description                                                 |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market        | string        | Yes     | Trading pair market [forecast: prediction market]           |
| symbol        | string        | No      | Trading pair code, e.g., `2434164_FORECAST` When `status=unsettled`, not specifying symbol will return all unsettled orders of all trading pairs When `status=settled`, the symbol parameter is required |

## Cancel Order

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
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

END_POINT = 'https://api.ktx.com/papi'
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
[n] // Number of cancelled orders
```

**Cancel an Order by ID**

* Request Method: POST

* Request Path: /v1/order/delete

* Permissions: Trade

* Request Parameters

| Parameter Name | Parameter Type | Required | Description                                                 |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id            | string        | Yes     | Order ID, supports multiple IDs concatenated The order ID can be assigned by the exchange, or customized by the user (using the client_order_id parameter when submitting the order). When using a custom ID, you need to add the "c:" prefix before the ID. For example: if you used custom ID "123" when submitting the order, use "c:123" when canceling the order. |


## Get Positions

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'market=forecast&symbol=2434164_FORECAST';
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + queryStr, secret).toString();
const url = `${endpoints}/v1/positions?${queryStr}`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/positions'
    query_str = 'market=forecast&symbol=2434164_FORECAST'
    expire_time = str(int(time.time() * 1000) + 5000)
    # POST or DELETE replace query_str with body_str
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
    "entryPrice": "0.6", // Entry price (probability, range 0-1)
    "symbol": "2434164_FORECAST", // Trading pair code, format {marketId}_FORECAST
    "leverage": "1", // Leverage (fixed to 1 for prediction market)
    "maintMargin": "0.0050000000", // Maintenance margin rate
    "side": "short", // Position direction [long: yes | short: no]
    "quantity": "0.100", // Position quantity (number of contracts)
    "posMargin": "0.6", // Position margin (USDT)
    "marginMethod": "isolate", // Margin mode (fixed to isolate for prediction market)
    "closableQty": "0.100", // Closable quantity (number of contracts)
    "initMargin": "0.1000000000", // Initial margin rate
    "id": "1125899906842624158", // Position ID
    "orderMargin": "0", // Order margin (USDT)
    "mergeMode": "short", // Position merge mode [long: merge yes positions | short: merge no positions]
    "introduction": "Bitcoin Up or Down - June 6, 3AM ET", // Event description
  }
  ...
]
```

**Get Positions**

* Request Method: GET

* Request Path: /v1/positions

* Permissions: View

* Request Parameters

| Parameter Name | Parameter Type | Required | Description                                                 |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| position_id   | string        | No      | Position ID. If this parameter exists, it has the highest priority |
| market        | string        | Yes     | Trading pair market [forecast: prediction market]           |
| symbol        | string        | No      | Use with market parameter. Trading pair code, e.g., `2434164_FORECAST` |

## MergeSplit

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret

const param = {
    id:'14244173146202090'
}

let bodyStr = JSON.stringify(param);
const exprieTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ exprieTime + bodyStr, secret).toString();
const url = `${endpoints}/v1/splitMerge`;

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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'

def do_request():

    param = {
        'id': '14245272657638034'
    }
    body_str = json.dumps(param)
    expire_time = str(int(time.time() * 1000) + 5000)
    sign = hmac.new(SECRET_KEY.encode("utf-8"), ('' + expire_time + body_str).encode("utf-8"), hashlib.sha256).hexdigest()
    path = '/v1/splitMerge'
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
""
```

**MergeSplit**

* Request Method: POST

* Request Path: /v1/splitMerge

* Permissions: Trade

* Request Parameters

| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | string         | Yes      | Trading pair market [forecast: prediction market]  |
| symbol         | string         | No       | Trading pair code, e.g., `2434164_FORECAST`        |
| type           | string         | Yes      | split or merge                                     |
| quantity       | decimal        | Yes      | Quantity to split or merge                         |

## Get Fill Details

> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apikey = "9e03e8fda27b6e4fc6b29bb244747dcf64092996"; // your apikey
const secret = "b825a03636ca09c884ca11d71cfc4217a98cb8bf"; // your secret


const queryStr = 'limit=2&market=forecast&symbol=2434164_FORECAST';
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

END_POINT = 'https://api.ktx.com/papi'
API_KEY = '9e03e8fda27b6e4fc6b29bb244747dcf64092996'
SECRET_KEY = 'b825a03636ca09c884ca11d71cfc4217a98cb8bf'


def do_request():
    path = '/v1/fills'
    query_str = 'limit=2&market=forecast&symbol=2434164_FORECAST'
    expire_time = str(int(time.time() * 1000) + 5000)
    # POST or DELETE replace query_str with body_str
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
    "product": "2434164_FORECAST", // Trading pair code (prediction market format: {marketId}_FORECAST)
    "fees": [
      {
        "amount": "10", // Fee amount
        "asset": "USDT", // Fee asset code
        "value": "10" // Fee valuation (USDT)
      }
    ],
    "quantity": "0.01", // Filled quantity (spot/futures: base currency amount; prediction market: number of contracts)
    "orderId": "4611772879845982371", // Order ID
    "fillId": "1125899906842624338", // Fill ID
    "price": "1000000", // Filled price (spot/futures: price; prediction market: probability, range 0-1)
    "time": "1733541360859", // Fill time (millisecond timestamp)
    "taker": true, // Is Taker [true: taker | false: maker]
    "side": "buy", // Trading direction [buy: buy | sell: sell]
    "profit": "-9060", // Profit (positive: profit, negative: loss)
    "tradeId": 26 // Trade ID 
  }
]
```

**Get Fills**

* Request Method: GET
* Request Path: /v1/fills
* Permissions: View
* Request Parameters(requires sorting)

| Parameter Name | Parameter Type | Required | Description                                                  |
|-----------------|----------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market         | string         | Yes      | Trading pair market [forecast: prediction market]            |
| order_id       | string         | No       | Order ID assigned by the exchange Limit to return only fills for the specified order If this parameter is not specified, please specify symbol |
| symbol         | string         | No       | Trading pair code, e.g., `2434164_FORECAST` Limit to return only fills for the specified trading pair If this parameter is not specified, please specify order_id |
| start_time     | int64          | No       | Limit the earliest time of returned fills                    |
| end_time       | int64          | No       | Limit the latest time of returned fills                      |
| before         | int64          | No       | Fill ID, limit the maximum ID of returned fills              |
| after          | int64          | No       | Fill ID, limit the minimum ID of returned fills              |
| limit          | int32          | No       | Limit the maximum number of returned results Default value: 100 |


## Data Streams
* For public data subscriptions, you only need to replace the corresponding market and symbol in the format. The returned price data always represents the price for the YES direction. For example, the subscription for the order book is：{"method":"SUBSCRIBE","params":["forecast.2434164_FORECAST.order_book.5"]}
* User Private Data Push: The format of the pushed order and position data is identical to the response format of the order and position APIs requested in the prediction market interface above.
