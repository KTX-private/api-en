# User Data Endpoints




## Get Trade Account Asset


> Request

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'asset=BTC';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/trade/accounts?${queryStr}`;

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
        String path = "/v1/trade/accounts";
        String queryStr = "asset=BTC";
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

**Get the balance, freeze and other information of various assets in the corresponding account by API Key**

* Request method GET
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    coin_symbol:'sUSDT'
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/depositAddr`;

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
        String path = "/v1/depositAddr";
        String bodyStr = "{"coin_symbol":"sUSDT"}";
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

* Request method POST
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    coin_symbol:"BTC",
    amount:"0.001",
    addr:"bc1qksfjx5ezznnngk6grt04h8lwnta2mxtmjl0etm"
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/withdraw`;

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
        String path = "/v1/withdraw";
        String bodyStr = "{"coin_symbol":"BTC","amount":"0.001","addr":"bc1q..."}";
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

* Request method POST
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'asset=BTC';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/main/accounts?${queryStr}`;

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
        String path = "/v1/main/accounts";
        String queryStr = "asset=BTC";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    symbol:'USDT',
    amount: 10,
    type:'WALLET_TRADE'
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/transfer`;

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
        String path = "/v1/transfer";
        String bodyStr = "{"symbol":"USDT","amount":10,"type":"WALLET_TRADE"}";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
  'symbol':'BTC',
  'amount':'0.001',
  'sub_user_id':30000416,
  'side':'in',
  'transfer_id':'userdefineid001',
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/subaccount/transfer`;

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

END_POINT = 'https://api.ktx.com/api'
API_KEY = 'YOUR_API_KEY'
SECRET_KEY = 'YOUR_SECRET_KEY'

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
        String path = "/v1/subaccount/transfer";
        String bodyStr = "{"symbol":"BTC","amount":"0.001","sub_user_id":30000416,"side":"in"}";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'asset=BTC&end_time=1651895799668&limit=10';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString();
const url = `${endpoints}/v1/ledgers?${queryStr}`;

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
        String path = "/v1/ledgers";
        String queryStr = "asset=BTC&limit=10";
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

* Request method GET
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

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
        String bodyStr = "{"symbol":"BTC_USDT","side":"buy","quantity":"0.0001","price":"90000","type":"limit","market":"spot"}";
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
  "orderId": "4611767382287843330", // Order ID
  "clientOrderId": "", // Client Order ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT", // Trading pair
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
  "orderId": "4611767382287843330", // Order ID
  "clientOrderId": "", // Client Order ID
  "createTime": "1733390630904", // Creation time
  "product": "BTC_USDT_SWAP", // Trading pair code
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

* Request method GET
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'limit=2&market=spot&symbol=BTC_USDT';
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
    query_str = 'limit=2&market=spot&symbol=BTC_USDT'
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
        String queryStr = "limit=2&market=spot&symbol=BTC_USDT";
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
    "orderId": "4611767382287843330", // Order ID
    "clientOrderId": "", // Client Order ID
    "createTime": "1733390630904", // Creation time
    "product": "BTC_USDT_SWAP", // Trading pair code
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

* Request method GET
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'market=spot&symbol=BTC_USDT';
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
    query_str = 'market=spot&symbol=BTC_USDT'
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
        String queryStr = "market=spot&symbol=BTC_USDT";
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
    "orderId": "4611767382287843330", // Order ID
    "clientOrderId": "", // Client Order ID
    "createTime": "1733390630904", // Creation time
    "product": "BTC_USDT_SWAP", // Trading pair code
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

* Request method GET
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
  positionId: '1125899906842649789',
  leverage:20,
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/change/leverage`;

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
        String path = "/v1/change/leverage";
        String bodyStr = "{"positionId":"1125899906842649789","leverage":20}";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
  positionId: '1125899906842649789',
  amount: 12,
  type: 1,
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/margin/transfer`;

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
        String path = "/v1/margin/transfer";
        String bodyStr = "{"positionId":"1125899906842649789","amount":12,"type":1}";
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
        String bodyStr = "{"id":"14244173146202090"}";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";

const param = {
    symbol:'BTC_USDT',
    market:'spot'
}

let bodyStr = JSON.stringify(param);
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + bodyStr, secretKey).toString();
const url = `${endpoints}/v1/orders/delete`;

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
        String path = "/v1/orders/delete";
        String bodyStr = "{"symbol":"BTC_USDT","market":"spot"}";
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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'market=lpc&symbol=BTC_USDT_SWAP';
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
        String queryStr = "market=lpc&symbol=BTC_USDT_SWAP";
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
    "entryPrice": "109398.9", // Entry price
    "symbol": "BTC_USDT_SWAP", // Trading pair
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


* Request method GET
* Request path /v1/positions
* Permanent: View
* Request parameters

| Parameter Name | Parameter Type | Whether it must be passed | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|----------------|----------------| ---------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| position_id   | string   | No                        | Position ID, the main param                                                                 |
| market | string   | No                        | trading pair markets, such as spot, lpc, etc., spot is spot, Trading pair market [spot: spot; lpc: USDT-M perpetual] |
| symbol     | string   | No                        | use symbol with market param, trading pair code<br/>such as BTC_USDT_SWAP, ETH_USDT_SWAP |

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
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'limit=2&market=spot&symbol=BTC_USDT';
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
        String queryStr = "limit=2&market=spot&symbol=BTC_USDT";
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
  "product":"BTC_USDT_SWAP", // Trading pair code
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

* Request method GET
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

