Bex-official-api-docs
==================================================
Official Documentation for the [Bex][] APIs and Streams([简体中文版文档]())

<!-- TOC -->

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Encrypted Verification of API](#encrypted-verification-of-api)
    - [Generate an API Key](#generate-an-api-key)
    - [Initiate a Request](#initiate-a-request)
    - [Signature](#signature)
    - [Select timestamp](#select-timestamp)
    - [Request Process](#request-process)
        - [Request](#request)
        - [Pagination](#pagination)
    - [Standards and Specification](#standards-and-specification)
        - [Timestamp](#timestamp)
        - [For example,](#for-example)
        - [Numbers](#numbers)
        - [Rate Limits](#rate-limits)
            - [REST API](#rest-api)
- [Futures Contract Business API Reference](#futures-contract-business-api-reference)
    - [Futures Contract Market API](#Futures-Contract-Market-API)
        - [1. Get all currency pair information](#1-Get-all-currency-pair-information)
        - [2. Obtain the trading depth of the currency pair](#2-Obtain-the-trading-depth-the-currency-pair)
        - [3. Get the latest transaction record of a single contract](#3-Get-the-latest-transaction-record-of-a-single-contract)
    - [Futures Contract Account API](#Futures-Contract-Account-API)
        - [1. Order trading interface](#1-Order-trading-interface)
        - [2. Order cancellation](#2-Order-cancellation)
        - [3. Query orders in commission](#3-Query-orders-in-commission)
        - [4. Query detailed information of a single order](#4-Query-detailed-information-of-a-single-order)
        - [5. Get user account asset information](#5-Get-user-account-asset-information)
        - [6. Query user position information](#6-Query-user-position-information)
        - [7. Get System Time](#7-Get-System-Time)

<!-- /TOC -->

# Introduction

Welcome to [Bex][] API document for developers.

This document provides an introduction to the use of related APIs such as account management, market query, and trading functions for futures contract trading services.
The market API provides the market's public market data interface. The account and trading API require identity verification, and provide functions such as order placing, order cancellation, and query of order and account information.

# Getting Started    
REST, short for Representational State Transfer, is a popular Internet transmission architecture. It has a clear structure, conforms to standards, is easy to understand, and easy to expand, and is being adopted by more and more websites. The advantages are as follows:

+ In the RESTful architecture, each URL represents a resource;
+ Between the client and the server, some kind of presentation layer to transfer this kind of resource;
+ The client operates the server-side resources through four HTTP instructions to achieve "presentation state transition".

It is recommended that developers use REST APIs for futures contract transactions or asset withdrawal operations.

# Encrypted Verification of API
## Generate an API Key

Before signing any request, you must create an API key through Bex website [User Center]-[API]. After creating the key, you will get 2 pieces of information you must remember:
* API Key

* Secret Key


API Key and Secret Key will be randomly generated and provided.

## Initiate a Request

All REST requests must include the following parameters:

* API Key as a string.
* sign A signature derived from a certain algorithm (see signature information).
* timestamp as the timestamp of your request.
* All requests should contain content of type application / json and be valid JSON.

## Signature
The sign parameter is to sort all the parameters (including timestamp) according to the dictionary, and use the HMAC SHA256 method according to key1 = value1 + key2 = value2 ... + Secret Key ** string (+ means string connection) Encrypted.

* method is the request method (POST / GET / PUT / DELETE), all capital letters.


**For example: sign the following request parameters**

	```bash
	curl "https://spai.bex.io/api-web/v1/user/getBalance"
	      
	```
* Get the balance information of a user's asset, take apiKey = abcnothing, secretKey = efgkey123 as an example

		```java
		timestamp = 1540286290170
		apiKey = abcnothing
		currency = BTC
		```

After sorting by dictionary, for

	```java
	apiKey = abcnothing
	currency = BTC
	timestamp = 1540286290170
	```

Generate the string to be signed

	```
	originString = 'apiKey=abcnothing&currency=BTC&timestamp=1540286290170'
	  
	```

Then, add the private key parameter to the string to be signed to generate the final string to be signed.


example：

		```
		Signature = HmacSHA256(secretkey, originString)
		which is：
		Signature = HmacSHA256("efgkey123", "apiKey=abcnothing&currency=BTC&timestamp=1540286290170")
		
		```

If the signature result is aaaabbbbccccffffeeeeffff, then the url query parameter after signature is
	```java
	apiKey = ABC
	currency = BTC
	timestamp = 1540286290170
	sign = aabbbbccccffffeeeeffff
	
	That is, the API request finally sent to the server should be:
	"https://spai.bex.io/api-web/v1/user/getBalance?apiKey=abcnothing&currency=BTC&timestamp=1540286290170&sign=aabbbbccccffffeeeeffff"
	```

## Request interaction  

Root URL for REST access：`https://spai.bex.io`

### request

All requests are based on the Https protocol, and the Content-Type in the request header information must be uniformly set to: 'application / json ’.

**Request interactive instructions**

1. Request parameter: parameter encapsulation according to the interface request parameter.

2. Submit request parameters: Submit the encapsulated request parameters to the server through POST / GET / DELETE.

3. Server response: The server first performs parameter security verification on the user request data, and after verification, returns the response data to the user in JSON format according to business logic.

4. Data processing: processing the server response data.

**success**

HTTP status code 200 indicates a successful response and may contain content. If the response contains content, it will be displayed in the corresponding returned content.

**Common HTTP error codes**

* 4XX Error code is used to indicate wrong request content, behavior, format

* 5XX Error codes are used to indicate problems on the Bex service side

* 400 Bad Request – Invalid request format

* 401 Unauthorized – Invalid API Key

* 403 Forbidden – You do not have access to the requested resource

* 404 Not Found

* 429 Too Many Requests requests are too frequently restricted by the system

* 418 said to continue to visit after receiving 429, so was blocked

* 500 Internal Server Error – We had a problem with our server

* 504 indicates that the API server has submitted a request to the business core but failed to obtain a response. It is important to note that the 504 code does not mean that the request failed, but is unknown. It may have been executed, or it may have failed, requiring further confirmation

* If it fails, the response body has an error description

* Every interface may throw an exception


## Standard specification

### Timestamp

Unless otherwise specified, all timestamps in the API are returned in microseconds.

The timestamp of the request must be within 30 seconds of the API service time, otherwise the request will be considered expired and rejected. If there is a large deviation between the local server time and the API server time, then we recommend that you update the http header by querying the API server time.

### example

1524801032573

### digital

To maintain the integrity of accuracy across platforms, decimal numbers are returned as strings. It is recommended that you also convert numbers to strings when initiating requests to avoid truncation and precision errors.

Integers (such as transaction number and order) are not quoted.

### Limiting

If the request is too frequent, the system will automatically limit the request。

##### REST API

* Public interface: We restrict the calling of the public interface by IP: up to 6 requests every 2 seconds.

* Private interface: We restrict the calling of the private interface by user ID: up to 6 requests every 2 seconds.

* Special restrictions for certain interfaces are noted on specific interfaces

# Futures contract business API reference

## Futures contract market API

### 1. Get all trading pair information

**HTTP request**

	```http
	    # Request
	    GET api-web/v1/getAllContracts
	    
	    example： https://sapi.bex.io/api-web/v1/market/getAllContracts
	```


	```javascript
# Response
	   {
	   	"error": 0,
	   	"msg": "",
	   	"data": [{
	        "contractId": "100",
	   		"symbol": "BTC_USDT",
	   		"name": "BTC合约",
	   		"size": "0.0001",
	        "volumePrecision": 0,
	        "pricePrecision": 2,
	        "feeRate": 0.001,
	        "tradeMinLimit": 1,
	   		"currency": "USDT",
	   		"asset": "BTC"
	   	}, {
	        "contractId": "101",
	   		"symbol": "ETH_USDT",
	   		"name": "ETH合约",
	   		"size": "0.01",
	        "volumePrecision": 0,
	        "pricePrecision": 2,
	        "feeRate": 0.001,
	        "tradeMinLimit": 1,
	   		"currency": "USDT",
	   		"asset": "ETH"
	   	}],
	   	
	   	...
	   	
	   } 
	```



**Return value description**  


| Back Field | Field Description |
| ---------- |: -------: |
| error | Is there any error message, 0 is normal, 1 is error |
| msg | Error message description
| contractId | contract ID
| symbol | Contract product symbol, returned in the form of A_B |
| name | contract product name |
| size | Contract size, for example 0.0001 BTC |
| volumePrecision | Accuracy of transaction volume |
| pricePrecision | Price accuracy |
| feeRate | Transaction fees |
| tradeMinLimit | Trading minimum unit |
| currency | Settlement and Margin Currency Assets |
| asset | contract trading assets |

### 2. Get contract request list of market depth

    Get contract request list of market depth

**HTTP request**


	```http
	    # Request
	    GET api-web/v1/market/getMarketDepth
	```


**Request parameters**  


| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| symbol | String | Yes | Currency pairs, such as BTC_USDT |


```javascript
# Response
	  {
	  	"error": 0,
	  	"msg": "",
	  	"data": {
	        "asks": [[
	                "p": 5319.94,
	                "v": 0.05483456
	            ],[
	                "p": 5320.19,
	                "v": 1.05734545
	            ],[
	                "p": 5320.39,
	                "v": 1.16307999
	            ],[
	                "p": 5319.94,
	                "v": 0.05483456
	            ],[
	                "p": 5320.19,
	                "v": 1.05734545
	            ],[
	                "p": 5320.39,
	                "v": 1.16307999
	            ],
	        ],
	        "bids": [[
	                "p": 5319.94,
	                "v": 0.05483456
	            ],[
	                "p": 5320.19,
	                "v": 1.05734545
	            ],[
	                "p": 5320.39,
	                "v": 1.16307999
	            ],[
	                "p": 5319.94,
	                "v": 0.05483456
	            ],[
	                "p": 5320.19,
	                "v": 1.05734545
	            ],[
	                "p": 5320.39,
	                "v": 1.16307999
	            ],
	        ],
	  	}
	  }
	```
**Return value description**  

| Back Field | Field Description |
| ------------- | ---- |
| error | Is there any error message, 0 is normal, 1 is error |
| msg | Error message description
| asks | seller depth |
| bids | buyer depth |
| p | price | float64
| v | volume quantity | float64

### 3. Get the latest transaction record of a single contract

    Get the latest transaction record of a single contract

**HTTP request**

	  ```http
	    # Request
	    GET api-web/v1/market/getMarketTrades
	```

**Request parameters**

| Parameter name | Parameter type | Required | Field description | Description |
| ------- | -------- | --- | ------- | ------ |
| symbol | String | yes | contract name | contract name needs to be underlined (BTC_USDT) |

	   ```javascript
# Response
	     {
	        "error": 0,
	        "msg": "",
	        "data": {
	            "trades": [
	                {
	                    "time": "2018-04-25 15:00:51",
	                    "makerSide": "Bid",
	                    "price": 0.279563,
	                    "volume": 100,
	                },
	                {
	                    "time": "2018-04-25 15:00:51",
	                    "makerSide": "Ask",
	                    "price": 0.279563,
	                    "volume": 300,
	                }
	            ]
	        }
	    }
	   ```

   **Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| time | data | | transaction time |
| makerSide | String | | Single order (Bid / Ask buy / sell) |
| price | String | | Transaction price |
| volume | String | | Number of transactions |


  **Remarks**


    For more error codes, please see the error code description on the homepage

### 4. Get single fund rate history

    Get single fund rate history

**HTTP request**

	  ```http
	    # Request
	    GET api-web/v1/market/getHistoryFunding
	```

**Request parameters**

| Parameter name | Parameter type | Required | Field description | Description |
| ------- | -------- | --- | ------- | ------ |
| symbol | String | yes | contract name | contract name needs to be underlined (BTC_USDT) |

	   ```javascript
# Response
	     {
	        "error": 0,
	        "msg": "",
	        "data": {
	            "fundings": [
	                {
	                    "historyId": "687",
	                    "symbol": "ETH-USDT",
	                    "fundingRate": "0.3000",
	                    "fairPrice": "182.73",
	                    "interval": "8",
	                    "time": "2019-10-28 16:00:00"
	                },
	                {
	                    "historyId": "686",
	                    "symbol": "ETH-USDT",
	                    "fundingRate": "0.3000",
	                    "fairPrice": "182.90",
	                    "interval": "8",
	                    "time": "2019-10-28 15:00:00"
	                }
	            ]
	        }
	    }
   ```

   **Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| historyId | String | | History ID number |
| fundingRate | String | | Funding Rate |
| fairPrice | String | | Mark price |
| interval | String | | Capital rate settlement period, unit: hour |
| time | data | | settlement time |

  **Remarks**


    For more error codes, please see the error code description on the homepage

### 5. Get the latest K line data

    Get the latest K line data

**HTTP request**

	  ```http
	    # Request
	    GET api-web/v1/market/getLatestKline
	```

**Request parameters**

| Parameter name | Parameter type | Required | Field description | Description |
| ------- | -------- | --- | ------- | ------ |
| symbol | String | yes | contract name | contract name needs to be underlined (BTC_USDT) |
| klineType | String | Yes | kline type | kline type (minutes, hours, weeks, etc.) |

**Remarks**

| klineType field description | |
| ---------- | ---- |
| 1 | 1m one minute K line |
| 3 | 3m three minute K line |
| 5 | 5m 5 minutes K line |
15 | 15m fifteen minutes K line |
30 | 30m 30 minutes K line |
| 60 | 1h one hour K line |
| 120 | 2h two hours K line |
| 240 | 4h four hours K line |
| 360 | 6h 6 hours K line |
| 720 | 12h 12 hours K line |
| 1D | 1D Japanese K Line |
| 1W | 1W Weekly K Line |
| 1M | 1M month K line |

```javascript
# Response
    {
        "error": 0,
        "msg": "",
        "data": {
            "kline": {
                "ts": 1572253500000,
                "open": 181.41,
                "close": 181.54,
                "high": 181.54,
                "low": 181.39,
                "volume": 281
            }
        }
    }
   ```

**Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| open | float64 | | Opening price |
| close | float64 | | closing price |
| high | float64 | | Highest price |
| low | float64 | | Lowest price |
| volume | float64 | | Number of transactions |
| ts | int64 | | k-line timestamp in milliseconds |

**Remarks**

    For more error codes, please see the error code description on the homepage

### 6. Obtain K-line historical data

    Obtain K-line historical data

**HTTP request**

	  ```http
	    # Request
	    GET api-web/v1/market/getHistoryKlines
	```

**Request parameters**

| Parameter name | Parameter type | Required | Field description | Description |
| ------- | -------- | --- | ------- | ------ |
| symbol | String | yes | contract name | contract name needs to be underlined (BTC_USDT) |
| klineType | String | Yes | kline type | kline type (minutes, hours, weeks, etc.) |
| startTs | int64 | | Start timestamp in milliseconds |
| endTs | int64 | | End timestamp in milliseconds |

**Remarks**

| klineType field description | |
| ---------- | ---- |
| 1 | 1m one minute K line |
| 3 | 3m three minute K line |
| 5 | 5m 5 minutes K line |
15 | 15m fifteen minutes K line |
30 | 30m 30 minutes K line |
| 60 | 1h one hour K line |
| 120 | 2h two hours K line |
| 240 | 4h four hours K line |
| 360 | 6h 6 hours K line |
| 720 | 12h 12 hours K line |
| 1D | 1D Japanese K Line |
| 1W | 1W Weekly K Line |
| 1M | 1M month K line |

```javascript
# Response
    {
        "error": 0,
        "msg": "",
        "data": {
            "klines": [
                {
                    "ts": 1572253140000,
                    "open": 181.89,
                    "close": 181.97,
                    "high": 182.04,
                    "low": 181.89,
                    "volume": 2136
                },
                {
                    "ts": 1572253200000,
                    "open": 181.94,
                    "close": 181.72,
                    "high": 181.94,
                    "low": 181.72,
                    "volume": 965
                },
                {
                    "ts": 1572253260000,
                    "open": 181.69,
                    "close": 181.72,
                    "high": 181.72,
                    "low": 181.56,
                    "volume": 1245
                },
                {
                    "ts": 1572253320000,
                    "open": 181.72,
                    "close": 181.73,
                    "high": 181.81,
                    "low": 181.69,
                    "volume": 541
                },
                {
                    "ts": 1572253380000,
                    "open": 181.77,
                    "close": 181.59,
                    "high": 181.77,
                    "low": 181.53,
                    "volume": 933
                },
                {
                    "ts": 1572253440000,
                    "open": 181.59,
                    "close": 181.38,
                    "high": 181.62,
                    "low": 181.38,
                    "volume": 1425
                },
                {
                    "ts": 1572253500000,
                    "open": 181.41,
                    "close": 181.64,
                    "high": 181.64,
                    "low": 181.39,
                    "volume": 923
                }
            ]
        }
    }
   ```

**Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| klines | array | | K-line data |
| open | float64 | | Opening price |
| close | float64 | | closing price |
| high | float64 | | Highest price |
| low | float64 | | Lowest price |
| volume | float64 | | Number of transactions |
| ts | int64 | | k-line timestamp in milliseconds |


**Remarks**

    For more error codes, please see the error code description on the homepage

## Trading API


### 1. Order trading interface

     Order transaction

**HTTP request**

     
           
```http
    # Request
    POST api-web/v1/user/trade
```
**Request method**

    POST

**Request parameters**  

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| symbol | String | yes | contract symbol (BTC_USDT) |
| apiKey | String | Yes | Interface Key |
| side | String | Yes | (Bid / Ask buy / sell) |
| entrustPrice | float64 | Yes | Price |
| entrustVolume | float64 | Yes | Quantity |
| tradeType | String | Yes | Market / Limit Market / Limit Price |
| action | String | Yes | Open / Close Open / Close |


```javascript
# Response
    {
        "error": 0,
        "msg": "",
        "data": {
            "orderId": "11141",
        }
    }
```

**Return value description**  

| Parameter name | Parameter type | Required | Description |
| ---- | ---- | ---- | ---- |
| orderId | String | Yes | Order ID |



### 2. Cancel order
 
       Cancel order  

**HTTP request**
 
   
   
```http
    # Request
    POST api-web/v1/user/cancelOrder
```

**Request method**

    POST

**Request parameters**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| orderId | String | Yes | Order ID |
| symbol | String | yes | contract symbol (BTC_USDT) |
| apiKey | String | Yes | Interface Key |


```javascript
# Response
    {
        "error": 0,
        "msg": "",
        "data": {
            "orderId": "11141",
        }
    }
```
**Return value description**

| Parameter name | Parameter type | Required | Description |
| ---- | ---- | ---- | ---- |
| orderId | String | Yes | Order ID |


### 3. Query orders in commission

    Query orders in commission

**HTTP request**

```http
    # Request
    POST api-web/v1/user/pendingOrders
```

**Request method**

    POST

**Request parameters**  

| Parameter name | Parameter type | Required | Field description
| ------------- | ---- | ---- | ---- |
| symbol | String | Yes | Contract Product (BTC_USDT) |
| apiKey | String | Yes | |

 ```javascript

# Response
    {
       "error": 0,
       "msg": "",
       "data": {
            "orders": [
                {
                    "entrustTm": "2018-04-25 15:00:51",
                    "side": "Bid",
                    "tradeType": "Limit",
                    "action": "Open",
                    "entrustPrice": 6.021954,
                    "entrustVolume": 18.098,
                    "filledVolume": 0,
                    "avgFilledPrice": 0,
                    "orderId": "6030"
                },
                {
                    "entrustTm": "2018-04-25 15:00:51",
                    "side": "Ask",
                    "tradeType": "Limit",
                    "action": "Close",
                    "entrustPrice": 6.021954,
                    "entrustVolume": 18.098,
                    "filledVolume": 0,
                    "avgFilledPrice": 0,
                    "orderId": "6030"
                },
            ]
        }
    }
 ```
 
 **Return value description**  

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| entrustTm | String | Yes | Order commission time |
| side | String | Yes | Trading direction (Bid / Ask buy / sell) |
| tradeType | String | Yes | Commission type (Market / Limit market price / limit price) |
| action | String | Yes | Open / Close Open / Close |
| entrustPrice | Float64 | Yes | Commissioned price |
| entrustVolume | Float64 | Yes | Number of Delegates |
| avgFilledPrice | Float64 | Yes | Average price |
filledVolume | Float64 | Yes | Number of transactions |
| orderId | String | yes | order number |

  **Remarks**
  
    For more error codes, please see the error code description on the homepage
  
### 4. Query the details of a single order
   
    Query the details of a single order

**HTTP request**

    ```http
    # Request
    POST api-web/v1/user/queryOrderStatus
    ```

**Request method**

    POST

**Request parameters**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| apiKey | String | Yes | Interface Key |
| symbol | String | yes | contract symbol (BTC_USDT) |
| orderId | String | Yes | Order ID |

```javascript
    # Response
     {
     	"error": 0,
     	"msg": "",
     	"data": {
     		"entrustTm": "2018-04-25 15:00:51",
            "side": "Ask",
            "tradeType": "Limit",
            "action": "Close",
            "entrustPrice": 6.021954,
            "entrustVolume": 18.098,
            "filledVolume": 0,
            "avgFilledPrice": 0,
            "orderId": "6030",
     		"status": "Filled"
     	}
     }
```

**Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| entrustTm | String | Yes | Order commission time |
| side | String | Yes | Trading direction (Bid / Ask buy / sell) |
| tradeType | String | Yes | Commission type (Market / Limit market price / limit price) |
| action | String | Yes | Open / Close Open / Close |
| entrustPrice | Float64 | Yes | Commissioned price |
| entrustVolume | Float64 | Yes | Number of Delegates |
| avgFilledPrice | Float64 | Yes | Average price |
filledVolume | Float64 | Yes | Number of transactions |
| orderId | String | yes | order number |
| status | String | Yes | Order status (Filled or PartiallyFilled, Pending, Cancelled, Failed) |
**Remarks**

| Status field description | |
| ---------- | ---- |
| Pending | Not yet completed |
| PartiallyFilled | Partial Deal |
| Cancelled | Cancelled |
| Filled | Completed |
| Failed | Failed |

  
For more error codes, please see the error code description on the homepage

### 5. Get user account asset information

    Get user account asset information
          
**HTTP request**
             
    ```http
        # Request
        POST api-web/v1/user/getBalance
    ```
**Request method**

        POST

**Request parameters**

| Parameter name | Parameter type | Required | Field description | Description |
| ------------- | ---- | ---- | --- | ---- |
| apiKey | String | Yes | Interface Key | |
| currency | String | yes | contract assets |

    ```javascript
        # Response
            {
                "error": 0,
                "msg": "",
                "data": {
                    "userId": "123",
                    "currency": "USDT",
                    "balance": 123.33,
                    "equity": 128.99,
                    "unrealisedPNL": 1.22,
                    "realisedPNL": 8.1,
                    "availableMargin": 123.33,
                    "usedMargin": 2.2,
                    "freezedMargin": 3.3,
                    "longLeverage": 10,
                    "shortLeverage": 10,
                }
            }
    ```
**Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| userId | String | Yes | User ID |
| currency | String | Yes | User Assets |
| balance | Float64 | Yes | Asset Balance |
| equity | Float64 | Yes | Net Asset Value |
| unrealisedPNL | Float64 | Yes | Unrealized P & L |
| realisedPNL | Float64 | Yes | Profit and Loss Realized |
| availableMargin | Float64 | Yes | Available Margin |
| usedMargin | Float64 | Yes | Used Margin |
| frozenMargin | Float64 | Yes | Frozen Margin |
| LongLeverage | Float64 | Yes | Long Leverage |
| shortLeverage | Float64 | Yes | Short Leverage |

**Remarks**


### 6. Query user position information

    Query user position information

**HTTP request**

```http
    # Request
    POST api-web/v1/user/getPositions
```

**Request method**

    POST

**Request parameters**  

| Parameter name | Parameter type | Required | Field description
| ------------- | ---- | ---- | ---- |
| symbol | String | Yes | Contract Product (BTC_USDT) |
| apiKey | String | Yes | |

 ```javascript

# Response
    {
       "error": 0,
       "msg": "",
       "data": {
            "positions": [
                {
                    "symbol": "BTC-USDT",
                    "currency": "USDT",
                    "positionSide": "Long",
                    "marginMode": "Cross",
                    "volume": 123.33,
                    "availableVolume": 128.99,
                    "unrealisedPNL": 1.22,
                    "realisedPNL": 8.1,
                    "margin": 123.33,
                    "avgPrice": 2.2,
                    "liquidatedPrice": 2.2,
                    "leverage": 10,
                },
                {
                    "symbol": "ETH-USDT",
                    "currency": "USDT",
                    "positionSide": "Short",
                    "marginMode": "Isolated",
                    "volume": 123.33,
                    "availableVolume": 128.99,
                    "unrealisedPNL": 1.22,
                    "realisedPNL": 8.1,
                    "margin": 123.33,
                    "avgPrice": 2.2,
                    "liquidatedPrice": 2.2,
                    "leverage": 10,
                },
            ]
        }
    }
 ```
 
**Return value description**  

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| symbol | String | Yes | Contract Type |
| currency | String | Yes | User Assets |
| positionSide | String | Yes | Position direction Long / Short Long / Short |
| marginMode | String | Yes | Margin Mode Cross / Isolated Full Position / Position by Position |
| volume | Float64 | Yes | Number of positions |
| availableVolume | Float64 | Yes | Number of open positions |
| unrealisedPNL | Float64 | Yes | Unrealized P & L |
| realisedPNL | Float64 | Yes | Profit and Loss Realized |
| margin | Float64 | Yes | Margin |
| avgPrice | Float64 | Yes | Average open price |
| liquidatedPrice | Float64 | Yes | Estimated strong parity |
| leverage | Float64 | yes | leverage |

**Remarks**
  
    For more error codes, please see the error code description on the homepage
    
### 7. Get system time

    Get system time
 
**HTTP request**

  ```http
        # Request
        POST api-web/v1/server/time
  ```
  
**Request method**

    GET / POST

               
**Request parameters**  
  
    no   
             
   ```javascript
    # Response
        {
            "error": 0,
            "msg": "",
            "currentTime": 1534431933321
        }
   ```
           
**Return value description**

| Parameter name | Parameter type | Required | Description |
| ------------- | ---- | ---- | ---- |
| currentTime | Int64 | Yes | Current system time in milliseconds |


**Remarks**

         
    For more error codes, please see the error code description on the homepage

    
[Bex]: https://www.bex.io
[English Docs]: https://github.com/bex-dev/official-api-docs-master/blob/master/README.md
[Unix Epoch]: https://en.wikipedia.org/wiki/Unix_time
