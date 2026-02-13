---
name: binance-api
description: Comprehensive guide for Binance Spot and Futures APIs. Use when the user asks about Binance API endpoints, authentication (HMAC-SHA256), trading protocols, market data, or code examples for Python/JavaScript. Covers both Spot (api.binance.com) and USDS-M Futures (fapi.binance.com).
---

# Binance API Skill

This skill provides expert knowledge on the Binance Spot and Futures (USDS-M) APIs.

## Base URLs

- **Spot API**: `https://api.binance.com`
- **USDS-M Futures API**: `https://fapi.binance.com`

## Authentication & Signing

Most private endpoints (Trading, Account) require HMAC-SHA256 signatures.

**Headers Required**:
- `X-MBX-APIKEY`: Your API Key.
- `Content-Type`: `application/x-www-form-urlencoded` (for POST/PUT).

**Signature Process**:
1. Construct the query string (e.g., `symbol=BTCUSDT&side=BUY&...&timestamp=1600000000000`).
2. Order parameters alphabetical order is NOT required for signature, but best practice is to keep them organized.
3. Compute `HMAC-SHA256(queryString, secretKey)`.
4. Append `&signature=BASE64_HEX_OF_HMAC` to the query string.
   *(Note: Binance expects valid hex string output of the HMAC, not Base64)*.

### Python Signing Example

```python
import hmac
import hashlib
import time
import requests
from urllib.parse import urlencode

API_KEY = "your_api_key"
SECRET_KEY = "your_secret_key"
BASE_URL = "https://api.binance.com" # or https://fapi.binance.com

def send_signed_request(method, path, params=None):
    if params is None:
        params = {}
    
    # 1. Add timestamp
    params["timestamp"] = int(time.time() * 1000)
    
    # 2. Prepare query string
    query_string = urlencode(params)
    
    # 3. Sign
    signature = hmac.new(
        SECRET_KEY.encode("utf-8"),
        query_string.encode("utf-8"),
        hashlib.sha256
    ).hexdigest()
    
    # 4. Append signature
    url = f"{BASE_URL}{path}?{query_string}&signature={signature}"
    
    headers = {"X-MBX-APIKEY": API_KEY}
    
    response = requests.request(method, url, headers=headers)
    return response.json()

# Example usage
# print(send_signed_request("POST", "/api/v3/order", {"symbol": "BTCUSDT", "side": "BUY", "type": "LIMIT", ...}))
```

### JavaScript (Node.js) Signing Example

```javascript
const crypto = require('crypto');
const axios = require('axios');

const API_KEY = 'your_api_key';
const SECRET_KEY = 'your_secret_key';
const BASE_URL = 'https://api.binance.com';

async function sendSignedRequest(method, path, params = {}) {
  params.timestamp = Date.now();
  
  // Convert object to query string
  const queryString = new URLSearchParams(params).toString();
  
  const signature = crypto
    .createHmac('sha256', SECRET_KEY)
    .update(queryString)
    .digest('hex');
    
  const url = `${BASE_URL}${path}?${queryString}&signature=${signature}`;
  
  try {
    const response = await axios({
      method: method,
      url: url,
      headers: { 'X-MBX-APIKEY': API_KEY }
    });
    return response.data;
  } catch (error) {
    console.error(error.response ? error.response.data : error.message);
  }
}
```

## Error Handling

- **HTTP 403 (WAF Limit)**: You are violating WAF rules. Don't retry immediately.
- **HTTP 429 (Rate Limit)**: Back off. Check `Retry-After` header.
- **HTTP 418 (IP Ban)**: You have been auto-banned for repeated 429s.
- **Code -1021 (Timestamp)**: `timestamp` is outside the `recvWindow` (default 5000ms). Sync your system clock.
- **Code -2010 (Account)**: Insufficient balance or other validation failure.

## API References

For specific endpoint details, refer to:

- **Spot API**: [Spot Reference](references/spot.md)
  - Market Data, account info, placing orders (Standard Limit/Market/OCO).
  
- **Futures API**: [Futures Reference](references/futures.md)
  - USDS-M Futures trading, Position Risk, Leverage, Mark Price.

## Common Rate Limits

- **Spot Order Rate**: Limits counted per 10s (e.g., 100 requests/10s).
- **Futures Order Rate**: Limits counted per 10s and 1m.
- **Request Weight**: Each endpoint has a weight (usually 1 for simple reads, higher for orders/heavy queries). Headers `X-MBX-USED-WEIGHT` tell you your usage.
