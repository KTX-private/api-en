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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/ping";
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

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/time'
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
        String path = "/v1/time";
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
            return console.error('request failed:', err);
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/coins";
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

* Request method GET
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/products?market=spot&symbol=BTC_USDT";
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
[{
	"feeMode": 0, // Fee mode [0: Unified fee rate]
	"symbol": "BTC_USDT", // Trading pair
	"takerFee": "0.002", // Taker fee rate
	"minOrderSize": "0.00001", // Minimum order quantity
	"mini": 0, // Mini contract flag [0: No | 1: Yes]
	"quantityIncrement": "0.000001", // Minimum quantity increment (step size)
	"profitSharing": "0", // Profit sharing ratio
	"priceIncrement": "0.01", // Minimum price increment (tick size)
	"active": 1, // Activation status [0: Disabled | 1: Enabled]
	"maxOrderValue": "10000000000", // Maximum order value
	"market": "spot", // Market type [spot: Spot | lpc: U-margined Contract | forecast: Forecast]
	"followFundingRate": 0, // Follow funding rate flag [0: No | 1: Yes]
	"makerFee": "0.001", // Maker fee rate
	"quantityScale": 6, // Quantity precision (decimal places)
	"priceScale": 2, // Price precision (decimal places)
	"maxOrderSize": "10000", // Maximum order quantity
	"fundingRateEx": "0", // Funding rate (extended field)
	"id": 1, // ID
	"time": "1781685210520", // Timestamp (milliseconds)
	"minOrderValue": "10" // Minimum order value
}]
```

**Get the product list**

* Request method GET
* Request path /v1/products
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                      |
|--------|----------------|------|--------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| Symbol | string         | No | Trading pair code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc.                              |

* Data source

Cache


## Get Leverage and Margin Tiers Info


> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/pu/getPositionTierRules  `
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
    path = '/v1/pu/getPositionTierRules'
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
        String path = "/v1/pu/getPositionTierRules";
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
  "EOS_USDT_SWAP": [
    {
      "no": 1,    // no
      "positionValue": "1000000", // position value<= 1000000
      "maintainMarginRate": "0.005", // maintain margin rate
      "initMarginRate": "0.01", // init  margin rate
      "maxLeverage": "100" // max leverage
    },
    {
      "no": 2,
      "positionValue": "2000000",
      "maintainMarginRate": "0.0075",
      "initMarginRate": "0.015",
      "maxLeverage": "67"
    },
    ...
  ],
  "BTC_USDT_SWAP": [
    ...
  ],
  "ETH_USDT_SWAP": [
    ...
  ],
  ...
}
```

**Get Leverage and Margin Tiers Info**

* Request method GET
* Request path /v1/pu/getPositionTierRules



## Get Order Book


> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/order_book?market=spot&symbol=BTC_USDT`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('request failed:', err);
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/order_book?market=spot&symbol=BTC_USDT";
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

* Request method GET
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
            return console.error('request failed:', err);
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/candles?market=spot&symbol=BTC_USDT&time_frame=1m";
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

* Request method GET
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
            return console.error('request failed:', err);
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/trades?market=spot&symbol=BTC_USDT";
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

* Request method GET
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
2. Use the tradeId of the first record as the value of the before parameter, and repeatedly use the symbol + before + limit parameter combination to get more records until all transaction records within three months are obtained

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
            return console.error('request failed:', err);
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

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class KtxApiExample {
    static final String ENDPOINT = "https://api.ktx.com/api";

    public static void main(String[] args) throws Exception {
        String path = "/v1/ticker?market=spot&symbol=BTC_USDT";
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
  "askPrice": "98100", // Ask price
  "product": "BTC_USDT", // Trading pair
  "amount": "922635", // 24h trading value
  "Last": "98000", // Latest transaction price
  "firstTradeId": 1, // First trade ID
  "change": "0", // 24h price change
  "bidQty": "1.7", // Bid quantity
  "bidPrice": "98000", // Bid price
  "volume": "9.41", // 24h trading volume
  "Lastqty": "0.3", // Last trade quantity
  "askqty": "0.5", // Ask quantity
  "high": "98100", // 24h highest price
  "tradeCount": 30, // Number of trades
  "Low": "98000", // 24h lowest price
  "time": "1733474204000", // Time
  "open": "98000" // Opening price
  }
]
```

**Get the quotation data**

* Request method GET
* Request path /v1/ticker
* Request parameters


| Parameter name | Parameter type | Whether to pass it? | Description                                                                                                                                                          |
|----------------|----------------| ---------- |----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| market | string         | No                        | trading pair markets, such as spot(default), lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol         | string         | Yes | Trading code, such as BTC_USDT, ETH_USDT, BTC_USDT_SWAP etc. <br/> You can specify multiple transactions in the following two forms <br/> 1.symbol=BTC_USDT,ETH_USDT |

* Data Source

Cache



## Get Contracts Summary


> Request

```javascript
let request = require("request");
const endPoint = 'https://api.ktx.com/api';
const url = `${endPoint}/v1/pu/contracts`
request.get(url,
        function optionalCallback(err, httpResponse, body) {
          if (err) {
            return console.error('request failed:', err);
          }

          console.log(body)

        });
```

```python
import requests

END_POINT = 'https://api.ktx.com/api';

def do_request():
    path = '/v1/pu/contracts'
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
        String path = "/v1/pu/contracts";
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
  "result": [
    {
      "ticker_id": "LAB_USDT_SWAP", // Trading pair
      "base_currency": "LAB", // Base currency
      "quote_currency": "USDT", // Quote currency
      "last_price": "12.22982", // Last price
      "base_volume": "263289681", // 24h base volume
      "quote_volume": "3682779280.73635", // 24h quote volume
      "bid": "12.22982", // Bid price
      "ask": "12.22987", // Ask price
      "high": "17.44572", // 24h high
      "low": "10.86188", // 24h low
      "product_type": "Perpetual", // Product type
      "open_interest": "2052", // Open interest
      "open_interest_usd": "25713.16056", // Open interest value (USDT)
      "index_price": "12.22733", // Index price
      "funding_rate": "-0.015491", // Funding rate
      "next_funding_rate_timestamp": "1783422000000" // Next funding rate timestamp
    },
    ...
  ]
}
```

**Get contract open interest, funding rate, index price and order book summary**

* Request method GET
* Request path /v1/pu/contracts
* Request parameters

None

* Data Source

Cache
