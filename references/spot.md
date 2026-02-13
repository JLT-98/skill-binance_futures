# Binance Spot API Reference

Base URL: `https://api.binance.com`

## Market Data Endpoints

### GET /api/v3/depth
**Description**: Get order book depth.
**Auth**: None (Public)
**Weight**: Adjusts based on limit (1-100: 1, 500: 5, 1000: 10, 5000: 50).

**Params**:
- `symbol` (Required): e.g., "BTCUSDT"
- `limit` (Optional): Default 100; max 5000.

**Response**:
```json
{
  "lastUpdateId": 1027024,
  "bids": [
    ["4.00000000", "431.00000000"] // [Price, Quantity]
  ],
  "asks": [
    ["4.00000200", "12.00000000"]
  ]
}
```

### GET /api/v3/avgPrice
**Description**: Current average price for a symbol.
**Auth**: None
**Weight**: 1

**Params**:
- `symbol` (Required)

**Response**:
```json
{ "mins": 5, "price": "9.35751834" }
```

## Account and Trading

### POST /api/v3/order (New Order)
**Description**: Send a new order.
**Auth**: SIGNED (API Key + Signature)
**Weight**: 1

**Mandatory Params**:
- `symbol` (str): "BTCUSDT"
- `side` (enum): BUY, SELL
- `type` (enum): LIMIT, MARKET, STOP_LOSS, TAKE_PROFIT, etc.

**Conditional Params**:
- `timeInForce` (enum): GTC, IOC, FOK (Required for LIMIT)
- `quantity` (decimal): Base asset quantity.
- `price` (decimal): Required for LIMIT.
- `quoteOrderQty` (decimal): Quote asset quantity (for MARKET BUY).

**Example Request (Python)**:
```python
params = {
    "symbol": "BTCUSDT",
    "side": "BUY",
    "type": "LIMIT",
    "timeInForce": "GTC",
    "quantity": 0.001,
    "price": 50000
}
# Sign and send (see SKILL.md for signing logic)
```

**Response**:
```json
{
  "symbol": "BTCUSDT",
  "orderId": 28,
  "clientOrderId": "6gCrw2kRUAF9CvJDGP16IP",
  "transactTime": 1507725176595,
  "price": "50000.00000000",
  "origQty": "0.00100000",
  "executedQty": "0.00000000",
  "cummulativeQuoteQty": "0.00000000",
  "status": "NEW",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "side": "BUY",
  "fills": []
}
```

### GET /api/v3/account
**Description**: Get current account information (balances).
**Auth**: SIGNED
**Weight**: 20

**Params**: `timestamp`.

**Response**:
```json
{
  "makerCommission": 15,
  "takerCommission": 15,
  "buyerCommission": 0,
  "sellerCommission": 0,
  "canTrade": true,
  "canWithdraw": true,
  "canDeposit": true,
  "updateTime": 123456789,
  "accountType": "SPOT",
  "balances": [
    {
      "asset": "BTC",
      "free": "4723846.89208129",
      "locked": "0.00000000"
    },
    {
      "asset": "LTC",
      "free": "4763368.68006011",
      "locked": "0.00000000"
    }
  ]
}
```
