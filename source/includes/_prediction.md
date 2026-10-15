# Prediction Market

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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/pu/events?market=forecast";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(ENDPOINT + path))
                .GET()
                .build();
        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/pu/events?id=2434164";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(ENDPOINT + path))
                .GET()
                .build();
        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/products?market=forecast&symbol=2434164_FORECAST";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(ENDPOINT + path))
                .GET()
                .build();
        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/ticker?market=forecast&symbol=2434164_FORECAST";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(ENDPOINT + path))
                .GET()
                .build();
        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/order_book?market=forecast&symbol=2434164_FORECAST";
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(ENDPOINT + path))
                .GET()
                .build();
        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

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
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/order`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'


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

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/order";
        String bodyStr = "{"symbol":"2434164_FORECAST","side":"buy","quantity":"1","price":"0.5","type":"limit","market":"forecast"}";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + bodyStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .POST(HttpRequest.BodyPublishers.ofString(bodyStr))
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'id=4611772879845982339';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/order?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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
    resp = requests.get(END_POINT + path, params=query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/order";
        String queryStr = "id=4611772879845982339";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + queryStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path + "?" + queryStr);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .GET()
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'limit=2&market=forecast&symbol=2434164_FORECAST';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/history/orders?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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
    resp = requests.get(END_POINT + path, params=query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/history/orders";
        String queryStr = "limit=2&market=forecast&symbol=2434164_FORECAST";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + queryStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path + "?" + queryStr);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .GET()
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'market=forecast&symbol=2434164_FORECAST';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/pending/orders?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime  
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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
    resp = requests.get(END_POINT + path, params=query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/pending/orders";
        String queryStr = "market=forecast&symbol=2434164_FORECAST";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + queryStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path + "?" + queryStr);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .GET()
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    id:'14244173146202090'
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/order/delete`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/order/delete";
        String bodyStr = "{"id":"14244173146202090","market":"forecast"}";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + bodyStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .POST(HttpRequest.BodyPublishers.ofString(bodyStr))
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'market=forecast&symbol=2434164_FORECAST';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/positions?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime 

        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'


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
    resp = requests.get(END_POINT + path, params=query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/positions";
        String queryStr = "market=forecast&symbol=2434164_FORECAST";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + queryStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path + "?" + queryStr);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .GET()
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    id:'14244173146202090'
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/splitMerge`;

request.post({
        url:url,
        body:param,
        json:true,
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime
        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/splitMerge";
        String bodyStr = "{"symbol":"2434164_FORECAST","action":"split","quantity":"1"}";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + bodyStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .POST(HttpRequest.BodyPublishers.ofString(bodyStr))
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'limit=2&market=forecast&symbol=2434164_FORECAST';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/fills?${queryStr}`;

request.get(url,{
        headers: {
            'Content-Type': 'application/json',
            'api-key': apiKey,
            'api-sign': sign,
            'api-expire-time':expireTime 

        },
    },

    function optionalCallback(err, httpResponse, body) {
        if (err) {
            return console.error('request failed:', err);
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
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'


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
    resp = requests.get(END_POINT + path, params=query_str, headers=headers)
    print(resp.text)


if __name__ == '__main__':
    do_request()
```

```java
import javax.crypto.Mac;
import javax.crypto.spec.SecretKeySpec;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/papi";
    static final String API_KEY = "YOUR_API_KEY";
    static final String SECRET_KEY = "YOUR_SECRET_KEY";

    public static void main(String[] args) throws Exception {
        String path = "/v1/fills";
        String queryStr = "limit=2&market=forecast&symbol=2434164_FORECAST";
        long expireTime = System.currentTimeMillis() + 5000;
        String sign = hmacSha256("" + expireTime + queryStr, SECRET_KEY);

        URI uri = URI.create(ENDPOINT + path + "?" + queryStr);
        HttpRequest request = HttpRequest.newBuilder(uri)
                .header("Content-Type", "application/json")
                .header("api-key", API_KEY)
                .header("api-sign", sign)
                .header("api-expire-time", String.valueOf(expireTime))
                .GET()
                .build();

        HttpResponse<String> response = HttpClient.newHttpClient()
                .send(request, HttpResponse.BodyHandlers.ofString());
        System.out.println(response.body());
    }

    static String hmacSha256(String data, String secret) throws Exception {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(secret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(data.getBytes());
        StringBuilder hex = new StringBuilder();
        for (byte b : hash) hex.append(String.format("%02x", b));
        return hex.toString();
    }
}
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
