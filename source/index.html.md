---
title: Ktx API doc(v4)

language_tabs:
- javascript
- python
- java

toc_footers:
- <a href='https://www.ktx.com'>Start Trading</a>

includes:
- market_rest
- market_ws
- user_rest
- user_ws
- prediction
- errors
- changelog

search: true

code_clipboard: true

---
# Basic information


## API


**Use API to develop applications, you can obtain market data of Ktx, supporting spot, USDT-M perpetual and prediction market. The API contains many interfaces, which are roughly divided into the following groups according to their functions:**

* Market Data Endpoints is used to obtain the REST interface of the market data
* User Data Endpoints is used to obtain the REST interface of the user's private data
* Market Data Streams WebSocket interface used to obtain market data
* User Data Streams is used to obtain the WebSocket interface that obtains the user's private data

**API uses the following Base URL:**

* Market Data Endpoints: https://api.ktx.com/api
* User Data Endpoints: https://api.ktx.com/papi (the old URL ended with /api and is no longer supported, please use /papi instead)
* Market Data Stream: wss://m-stream.ktx.com
* User Data Stream: wss://u-stream.ktx.com

**Backup API domain list**

[https://api.ktx.com/api/v1/pu/domains](https://api.ktx.com/api/v1/pu/domains)

**API's REST interface uses the following http method:**

* GET is used to obtain market or private data

* POST is used to submit orders and other operations
  **The parameters required for the REST interface of the API should be attached to the request according to the following rules:**

* The interface parameters of GET type should be attached to Query String

* The interface parameters of POST type should be attached to the Request Body in JSON format
  **Response**

The API's response data is returned in JSON format. For the specific format, please refer to the description of each interface.

**Error**

The error of the API is returned in the following JSON format:

```json
{
  "state": -10001,
  "msg": "error message"
}
```

*Where, state represents the error code, msg contains the cause of the error or how to fix it. For specific error types, please refer to the [Error](#errors) chapter.*

**Time or Timestamp**

The time values involved in the API interface parameters and response data are UNIX time, and the unit is milliseconds.

---


## Traffic restriction


**Ktx applies the following access restrictions on requests from the same IP:**

1. Access Limits access frequency limit
2. Usage Limits CPU usage limit

* Access Limits access frequency limit
1. At the same IP, up to 10,000 requests per 10 seconds. Requests exceeding the limit will receive a -20007 error.
2. Users can send up to 10,000 requests at any frequency within 10 seconds. They can send about once every 10ms, or they can be sent 10,000 times in 1 second, and then wait for 9 seconds.
   <br/>
   <br/>
* Usage Limits
1. The same IP consumes up to 10,000 CPU time every 10 seconds. Requests that exceed the limit will receive a -20006 error.
2. Different APIs consume different CPU time, which depends on how the API accesses the data.
3. In this article, the method of accessing the data of each API interface will be marked in the form of "cache" and "database". The CPU consumed by the cache API has less time, and the CPU consumed by the API consumed by the database is more time. According to the parameters sent by the user, the API may mix the cache and database, and even access the database multiple times, which will increase the CPU time consumed by the API.
4. The CPU time consumed by the API request will be included in Ktx-Usage in the response header. The format is T1: T2: T3. Among them, T1 represents the CPU time consumed by the API request. T2 represents the current IP within 10 seconds in the last 10 seconds The CPU time consumed, T3 indicates that the current IP remains available CPU time in the last 10 seconds.

---


## Authentication


> Complete Example

```javascript
let CryptoJS = require("crypto-js");
let request = require("request");

const endpoints = 'https://api.ktx.com/papi'
const apiKey = "YOUR_API_KEY";
const secretKey = "YOUR_SECRET_KEY";


const queryStr = 'asset=BTC';
const expireTime = Date.now()+5000;
const sign = CryptoJS.HmacSHA256(''+ expireTime + queryStr, secretKey).toString(); // POST or DELETE  replace queryStr with bodyStr
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
          console.log(body)

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
    # POST or DELETE replace query_str with body_str
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
        // POST replace queryStr with bodyStr
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

**Identity verification**

* Private interfaces are used to access account and order information. When making requests, additional signatures need to be added for Ktx authentication. This section will describe how to create signatures.

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

