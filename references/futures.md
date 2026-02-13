# Binance Futures (USDS-M) API Reference

Base URL: `https://fapi.binance.com`

## Market Data Endpoints

### GET /fapi/v1/premiumIndex
**Description**: Get Mark Price and Funding Rate.
**Auth**: None
**Weight**: 1

**Params**:
- `symbol` (Optional in v1): If not sent, returns all symbols.

**Response**:
```json
{
  "symbol": "BTCUSDT",
  "markPrice": "11012.80409769",
  "indexPrice": "11013.12927273",
  "estimatedSettlePrice": "11012.80409769",
  "lastFundingRate": "0.00038246",
  "nextFundingTime": 1562569200000,
  "interestRate": "0.00010000",
  "time": 1562566020745
}
```

### GET /fapi/v1/klines
**Description**: Kline/candlestick bars for a symbol.
**Auth**: None
**Weight**: 1

**Params**:
- `symbol` (Required)
- `interval` (Required): 1m, 1h, 1d, etc.
- `limit` (Optional): Default 500.

## Account and Trading

### POST /fapi/v1/order
**Description**: Send in a new order.
**Auth**: SIGNED
**Weight**: 0 (UID rate limit applies)

**Mandatory Params**:
- `symbol` (str)
- `side` (enum): BUY, SELL
- `type` (enum): LIMIT, MARKET, STOP, STOP_MARKET, TAKE_PROFIT, TAKE_PROFIT_MARKET, TRAILING_STOP_MARKET.

**Conditional Params**:
- `positionSide` (enum): BOTH (One-way mode), LONG/SHORT (Hedge mode). Default BOTH.
- `timeInForce` (enum): GTC, IOC, FOK, GTX (Good Till Crossing - Post Only).
- `quantity` (decimal)
- `price` (decimal)
- `reduceOnly` (bool): "true" or "false".

**Response**:
```json
{
  "orderId": 2422967000,
  "symbol": "BTCUSDT",
  "status": "NEW",
  "clientOrderId": "testOrder",
  "price": "40000",
  "avgPrice": "0.00000",
  "origQty": "1",
  "executedQty": "0",
  "cumQuote": "0",
  "timeInForce": "GTC",
  "type": "LIMIT",
  "reduceOnly": false,
  "closePosition": false,
  "side": "BUY",
  "positionSide": "BOTH",
  "workingType": "CONTRACT_PRICE",
  "priceProtect": false
}
```

### GET /fapi/v2/positionRisk
**Description**: Get current position information.
**Auth**: SIGNED
**Weight**: 5

**Params**:
- `symbol` (Optional)

**Response**:
```json
[
  {
    "entryPrice": "0.00000",
    "marginType": "isolated",
    "isAutoAddMargin": "false",
    "isolatedMargin": "0.00000000",
    "leverage": "10",
    "liquidationPrice": "0",
    "markPrice": "6679.50671178",
    "maxNotionalValue": "20000000",
    "positionAmt": "0.000",
    "symbol": "BTCUSDT",
    "unRealizedProfit": "0.00000000",
    "positionSide": "BOTH",
    "notional": "0"
  }
]
```

### GET /fapi/v2/account
**Description**: Get account info (balances, positions, etc).
**Auth**: SIGNED
**Weight**: 5

**Response**: Contains `assets` list (balance, walletBalance, marginBalance) and `positions` list.
