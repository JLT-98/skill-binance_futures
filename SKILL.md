---
name: binance_futures
description: Interact with Binance USDS-Margined Futures API for market data and trading.
---

# Binance USDS-Margined Futures Skill

This skill allows agents to interact with the Binance USDS-Margined Futures API. It covers market data retrieval, account information, and order execution.

## Prerequisites

-   **API Key & Secret**: You must have a valid Binance API Key and Secret Key.
-   **Base URL**:
    -   Production: `https://fapi.binance.com`
    -   Testnet: `https://testnet.binancefuture.com`

## Authentication

Binance Futures API uses HMAC SHA256 for authentication.
Signed endpoints require `timestamp`, `signature`, and optional `recvWindow`.

### Python Helper for Signing

```python
import time
import hmac
import hashlib
import requests
from urllib.parse import urlencode

API_KEY = "YOUR_API_KEY"
API_SECRET = "YOUR_SECRET_KEY"
BASE_URL = "https://fapi.binance.com" # Use https://testnet.binancefuture.com for testnet

def get_headers():
    return {
        "X-MBX-APIKEY": API_KEY
    }

def send_signed_request(method, endpoint, params=None):
    if params is None:
        params = {}
    
    # timestamp is mandatory
    params['timestamp'] = int(time.time() * 1000)
    
    # create signature
    query_string = urlencode(params)
    signature = hmac.new(
        API_SECRET.encode('utf-8'),
        query_string.encode('utf-8'),
        hashlib.sha256
    ).hexdigest()
    
    params['signature'] = signature
    
    url = f"{BASE_URL}{endpoint}"
    
    try:
        if method == "GET":
            response = requests.get(url, headers=get_headers(), params=params)
        elif method == "POST":
            response = requests.post(url, headers=get_headers(), params=params)
        elif method == "DELETE":
            response = requests.delete(url, headers=get_headers(), params=params)
        else:
            return {"error": "Unsupported method"}
            
        return response.json()
    except Exception as e:
        return {"error": str(e)}
```

## Market Data (Public Endpoints)

These endpoints do not require signing (except for higher rate limits sometimes, but usually public).

### 1. Exchange Information
Get all symbols and their trading rules.

-   **Endpoint**: `GET /fapi/v1/exchangeInfo`
-   **Usage**:
    ```python
    url = f"{BASE_URL}/fapi/v1/exchangeInfo"
    response = requests.get(url)
    data = response.json() 
    # print(data['symbols'])
    ```

### 2. Kline/Candlestick Data
Get historical price data (candles).

-   **Endpoint**: `GET /fapi/v1/klines`
-   **Parameters**:
    -   `symbol` (str): e.g., "BTCUSDT"
    -   `interval` (str): e.g., "1m", "1h", "1d"
    -   `limit` (int, optional): Default 500, max 1500.
-   **Usage**:
    ```python
    params = {"symbol": "BTCUSDT", "interval": "1h", "limit": 5}
    response = requests.get(f"{BASE_URL}/fapi/v1/klines", params=params)
    print(response.json())
    ```

### 3. Recent Trades
Get recent market trades.

-   **Endpoint**: `GET /fapi/v1/trades`
-   **Parameters**: `symbol`, `limit` (max 1000).

### 4. Order Book
Get order book depth.

-   **Endpoint**: `GET /fapi/v1/depth`
-   **Parameters**: `symbol`, `limit` (5, 10, 20, 50, 100, 500, 1000).

### 5. 24hr Ticker Price Change
Get 24hr rolling window price change statistics.

-   **Endpoint**: `GET /fapi/v1/ticker/24hr`
-   **Parameters**: `symbol` (optional, returns all if omitted).

### 6. Symbol Order Book Ticker
Best bid/ask price and quantity.

-   **Endpoint**: `GET /fapi/v1/ticker/bookTicker`
-   **Parameters**: `symbol` (optional).
-   **Usage**:
    ```python
    params = {"symbol": "BTCUSDT"}
    response = requests.get(f"{BASE_URL}/fapi/v1/ticker/bookTicker", params=params)
    print(response.json())
    ```

## Account & Trading (Signed Endpoints)

Use the `send_signed_request` helper function defined above.

### 1. Account Information
Get current account information (balances, positions).

-   **Endpoint**: `GET /fapi/v2/account`
-   **Usage**:
    ```python
    account_info = send_signed_request("GET", "/fapi/v2/account")
    print(account_info)
    # Check 'positions' field for open positions
    ```

### 2. New Order
Send a new order.

-   **Endpoint**: `POST /fapi/v1/order`
-   **Mandatory Parameters**:
    -   `symbol` (str): e.g., "BTCUSDT"
    -   `side` (str): "BUY" or "SELL"
    -   `type` (str): "LIMIT", "MARKET", "STOP", etc.
    -   `quantity` (float/str): Quantity to buy/sell.
-   **Conditional Parameters**:
    -   `price`: Required for LIMIT orders.
    -   `timeInForce`: Required for LIMIT orders (e.g., "GTC").
    -   `reduceOnly`: "true" or "false".
-   **Usage (Market Order)**:
    ```python
    params = {
        "symbol": "BTCUSDT",
        "side": "BUY",
        "type": "MARKET",
        "quantity": 0.001
    }
    order = send_signed_request("POST", "/fapi/v1/order", params)
    print(order)
    ```
-   **Usage (Limit Order)**:
    ```python
    params = {
        "symbol": "BTCUSDT",
        "side": "BUY",
        "type": "LIMIT",
        "quantity": 0.001,
        "price": 50000,
        "timeInForce": "GTC"
    }
    order = send_signed_request("POST", "/fapi/v1/order", params)
    print(order)
    ```

### 3. Query Order
Check an order's status.

-   **Endpoint**: `GET /fapi/v1/order`
-   **Parameters**: `symbol` AND (`orderId` OR `origClientOrderId`).
-   **Usage**:
    ```python
    params = {"symbol": "BTCUSDT", "orderId": 12345678}
    status = send_signed_request("GET", "/fapi/v1/order", params)
    print(status)
    ```

### 4. Cancel Order
Cancel an active order.

-   **Endpoint**: `DELETE /fapi/v1/order`
-   **Parameters**: `symbol` AND (`orderId` OR `origClientOrderId`).
-   **Usage**:
    ```python
    params = {"symbol": "BTCUSDT", "orderId": 12345678}
    cancel_response = send_signed_request("DELETE", "/fapi/v1/order", params)
    print(cancel_response)
    ```
