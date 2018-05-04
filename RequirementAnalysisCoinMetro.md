# CoinMetro Exchange NodeJS REST MicroService

This microservice receives/responds to all REST protocol requests from external REST based client applications. It uses redis pub/sub to receive/send messages (data) to and from the core microservice, messages received are cached with the redis cache.

## API Documentation


### HTTP Endpoints
- [User](#1-user)
    - [Signup](#11-user-signup)
    - [Email Confirmation](#12-email-confirmation)
    - [Login](#13-user-login)
**Comment from Salvatore**: _We already have this stuff up here in our existing REST API. This will have to be integrated somewhere, I would guess in the OData service. Maybe we can keep the login ? Or the JWTs can be pushed from our main server through the exchange server via OData. TBD_
- [Order](#2-order)
    - [Create Order](#21-create-order)
    - [Order Status](#22-order-status)
    - [Active Orders](#23-active-orders)
    - [Cancel Order](#24-cancel-order)
- [Market Data](#3-market-data)
    - [Ticker](#31-ticker)
    - [Market Depth](#32-market-depth)
    - [Price History](#33-price-history)
    

### 1. User

#### 1.1 User Signup
****
**Request Method:**  `POST`

**Endpoint:** `/rs/api/user/signup`

**Sample Request Payload:**
```json
    {
        "username": "fortunee",
        "email": "fortune@mail.com",
        "password": "pa$$w0rd"
    }
```
**Sample Success Response:**
```json
    {
        "success": "true",
        "message": "Signed up successfully. Please verify your account"
    }
```

**Sample Error Response:**
```json
    {
        "success": "false",
        "message": "Unable to signup"
    }
```


#### 1.2 Email Confirmation
****
**Request Method:**  `GET`

**Endpoint:** `/rs/api/user/confirm/:token`

**Sample Success Response:**
```json
    {
        "success": "true",
        "message": "Email confirmed successfully"
    }
```

**Sample Error Response:**
```json
    {
        "success": "false",
        "message": "Unable to confirm email. Please try again"
    }
```


#### 1.3 User Login
****
**Request Method:**  `POST`

**Endpoint:** `/rs/api/user/login`

**Sample Request Payload:**
```javascript
    {
        "username": "fortunee", // Email or username works
        "password": "pa$$w0rd"
    }
```

**Sample Success Response:**
```json
    {
        "success": "true",
        "token": "aikiewhiauhdnlakwe982y39uinaWYETO23",
    }
```

**Sample Error Response:**
```json
    {
        "success": "false",
        "message": "Unable to login. Please try again"
    }
```



### 2. Order
#### 2.1 Create Order
****

**Request Method:**  `POST`

**Endpoint:**  `/rs/api/order/`

**Sample Request Payload:**
```javascript
{ 
    "type": "buy", // Can be buy or sell
    "quantity": 2000000,
    "pair": "btcusd",
    "price": "10000.43",
    "comment": "Some comment about this order"
}
```

**Sample Success Response:** 
```json 
{
    "success": true,
    "orderId": "ORDER_ID_001",
}
```

**Sample Error Response:**
```json 
{
    "success": false,
    "message": "Unable to create order"
}
```

#### 2.2 Order Status
****

**Request Method:**  `GET`

**Endpoint:**  `/rs/api/order/:orderId`

**Sample Success Response 1:** 
```json 
{
    "success": true,
    "orderId": "ORDER_ID_001",
    "type": "buy",
    "timestamp": 1498828124,
    "status": "active",
    "pair": "btcusd",
    "price": 90000.43,
    "quantity": 0.33,
    "unitsFilled": 0.22,
    "comment": "Some comment about the order"
}
```

**Sample Success Response 2:** 
```json 
{
    "success": true,
    "orderId": "ORDER_ID_001",
    "type": "buy",
    "timestamp": 1498828124,
    "status": "closed",
    "pair": "btcusd",
    "price": 90000.43,
    "quantity": 0.33,
    "unitsFilled": 0.33,
    "comment": "Some comment about the order"
}
```

**Sample Success Response 3:** 
```json 
{
    "success": true,
    "orderId": "ORDER_ID_001",
    "type": "sell",
    "timestamp": 1498828124,
    "status": "cancelled",
    "pair": "btcusd",
    "price": 90000.43,
    "quantity": 0.33,
    "unitsFilled": 0.11,
    "comment": "Some comment about the order"
}
```

**Sample Error Response:**
```json 
{
    "success": false,
    "message": "Order does not exist"
}
```

#### 2.3 Active Orders
****

**Request Method:**  `GET`

**Endpoint:**  `/rs/api/order/active`

**Sample Success Response:** 
```json 
{
    "success": true,
    "orders":[
        {
            "orderId": "ORDER_ID_001",
            "type": "buy",
            "timestamp": 1498828124,
            "status": "active",
            "pair": "btcusd",
            "price": 90000.43,
            "quantity": 0.33,
            "unitsFilled": 0.22,
            "comment": "Some comment about the order"
        },
        {
            "orderId": "ORDER_ID_001",
            "type": "buy",
            "timestamp": 1498828124,
            "status": "closed",
            "pair": "btcusd",
            "price": 90000.43,
            "quantity": 0.33,
            "unitsFilled": 0.33,
            "comment": "Some comment about the order"
        }
    ]
}
```

**Sample Error Response:**
```json 
{
    "success": false,
    "message": "Unable to get Orders"
}
```

#### 2.4 Cancel Order
****

**Request Method:**  `POST`

**Endpoint:**  `/rs/api/order/cancel/:orderId`

**Sample Success Response:** 
```json 
{
    "success": true,
    "orderId": "ORDER_ID_001",
    "message": "Order successfully cancelled"
},
```

**Sample Error Response:**
```json 
{
    "success": false,
    "message": "Order does not exit"
}
```

### 3. Market Data
### 3.1 Ticker
****
**Request Method:**  `GET`

**Endpoint:** `/rs/api/ticker/?pairs=btcusd,ethusd&token=laiweeoaiwefabiuwefiIUYeiuaiwe`

**Sample Success Response:**

```json
{
    "success" : true,
    "btcusd": { 
      "timestamp": 1498860260,
      "last": 10962,
      "ask": 11062,
      "bid": 10962.27,
      "volume": 0.738
    },
    "ethusd": {
      "timestamp": 1498828124,
      "last": 952.50,
      "ask": 962.27,
      "bid": 952.50,
      "volume": 0.338
    }
}
```

**Sample Error Response:**
```json
{
  "success": false,
  "message": "Invalid authentication token"
}
```

### 3.2 Market Depth
****
**Request Method:**  `GET`

**Endpoint:** `/rs/api/market-depth/:pair`

**Sample Success Response:**

```json
{
    "success": true,
    "sell": [
        {
            "price": 8848.73542726,
            "quantity": 0.00788389
        },
        {
            "price": 8848.73542726,
            "quantity": 0.0922095,
        }
    ],

    "buy": [
        {
            "price": 8841.73542726,
            "quantity": 0.00788389
        },
        {
            "price": 8838.73542727,
            "quantity": 0.0922095,
        }
    ]
}
```

**Comment from Salvatore**: _This can be a huge bandwidth chugger. JSON must be optimized. Responses are probably gonna be relatively huge too, so gzip can help: <https://expressjs.com/en/advanced/best-practice-performance.html#use-gzip-compression>_

**Sample Error Response:**
```json
{
  "success": false,
  "message": "Market does not exist"
}
```


### 3.3 Price History
****
**Request Method:**  `GET`

**Endpoint:** `/rs/api/price/?pair=btcusd?interval=30minutes`

**Sample Success Response:**

```json
{
    "success": true,
    "btcusd":
      [
        {
          "timestamp": 1498828124,
          "last": 952.50,
          "ask": 962.27,
          "bid": 952.50,
          "volume": 0.338
        },
        {
          "timestamp": 146885900,
          "last": 952.50,
          "ask": 962.27,
          "bid": 952.50,
          "volume": 0.338
        },
        {
          "timestamp": 1438826200,
          "last": 952.50,
          "ask": 962.27,
          "bid": 952.50,
          "volume": 0.338
        }
      ]
}
```

**Comment from Salvatore**: _Historical data is almost always presented in a format [Time, Open, High, Low, Close]. This can be given for Ask and Bid, so it goes {Time, [Open, High, Low, Close], Ask: {Open, High, Low, Close]}. This can be a huge bandwidth chugger. JSON must be optimized. Responses are probably gonna be relatively huge too, so gzip can help: <https://expressjs.com/en/advanced/best-practice-performance.html#use-gzip-compression>_

**Sample Error Response:**
```json
{
  "success": false,
  "message": "Invalid interval specified"
}
```


## How the microservice is currently structured (Tentative)
### Routes
The routes are all tied to each of its controller action.

### Cache
Basically handles the caching of data received from the core service. However, it hasn't been organised properly as the structure of how messages will be published from the core service.

### Controllers
Handles client request/responses, if it is a `POST` request such as `newOrderRequest` the action basically publishes the request data to the Core Microservice, if it is a `GET` request such as `orderStatusRequest`, it fetches the order with the `orderId` that was passed in the `req.params` from the cache and if for some reason it is not found in the local cache it publishes that `orderId` to Core Microservice to publish the status of that particular order.
**Comment from Salvatore**: _What is the cache control criteria ? Can the Core Microservice publish a signal to bust the cache of the children Microservices, REST API in this case ? Otherwise we risk giving wrong infomation about the orders._

## Core Technologies Used
- Express
- Redis (Pub/Sub/Cache)

## How to setup locally
1. Within the `src` of the `CoinMetro.Services.REST`, run npm `install`
2. Ensure a `redis-server` is running
3. Run `npm run start:dev`
4. ...


## Unit testing
Currently, no test has been written but will be done soon.