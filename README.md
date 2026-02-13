PROMPT for AI agent create skill

🧠 **Prompt to Teach an AI Agent the Binance API Docs**

```
Using the skill `skill-creator` to create the skill pattern, create the skill to folder "C:\Users\jianl\.agents\skills"
You are an AI agent specialized in understanding API documentation and developer reference guides. Your task is to ingest, index, and develop capabilities (“skills”) around the following official Binance API documentation:

1. Binance Futures (USDT-Margined):
   https://developers.binance.com/docs/derivatives/usds-margined-futures/general-info

2. Binance Spot REST API:
   https://developers.binance.com/docs/binance-spot-api-docs/rest-api#market-data-endpoints

Your goal is to build an internal representation, search tools, and ready-to-use interaction capabilities that enable answering questions, generating example code, and explaining how to implement Binance API functions.

---

## 📌 **OBJECTIVES / TASKS**

### 1. **Ingest & Parse Documentation**
• Read and parse the full documentation at the two links above.
• Extract structured information including:
  - Endpoint names and descriptions
  - Required parameters (path, query, body)
  - Response schemas
  - Errors and status codes
  - Authentication/security requirements
  - Code examples (if present)

### 2. **Understand Binance Concepts**
• Spot API concepts:
  - Market Data
  - Account/Trading
  - Orders (limit, market, stop, OCO)
  - User data streams
  - Signing & security
• Futures API concepts:
  - Contracts, leverage, margins
  - Position and account information
  - Order types (FOK, IOC, etc.)
  - Funding rates, mark price, liquidation

### 3. **Build Useful Skills/Capabilities**
Your internal system should be able to answer questions and generate code like:

**Examples of capabilities**
• “Describe the Binance Spot order endpoint and show a Python example.”
• “How do I connect and authenticate with Binance Futures API?”
• “What’s the difference between SPOT and USDT Futures endpoints?”
• “Generate TypeScript code to query BTCUSDT candlestick data.”
• “Explain how to compute API signatures for signed endpoints.”

---

## 📝 **EXPECTED OUTPUT PATTERNS**

Whenever giving an answer about the API, the agent should provide:

1. **Endpoint name & description**
2. **HTTP method and full path**
3. **Required and optional parameters**
4. **Authentication level (Public / API-Key / Signed)**
5. **Sample request (Python + JavaScript)**
6. **Sample response object**
7. **Common error codes and how to handle them**
8. **Related endpoints or best practices**

Example answer format:
```

### Endpoint: GET /api/v3/ticker/price

Description: Gets the latest price for one or all symbols.

Method: GET

Auth: None

Params:

- symbol (optional)

Response:

{

"symbol": "BTCUSDT",

"price": "60000.00"

}

Python Example (requests):

```python
import requests
url = "https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT"
print(requests.get(url).json())
```

JavaScript Example (fetch):

```jsx
await fetch("https://api.binance.com/api/v3/ticker/price?symbol=BTCUSDT")
```

Related: GET /api/v3/ticker/bookTicker, GET /api/v3/exchangeInfo

```

---

## 🔐 **Authentication / Signing Logic**

The agent must also learn and be able to explain:

• API key header usage
• Signature generation (HMAC-SHA256)
• Timestamp and recvWindow usage
• Security best practices
• Rate limits & weight headers

Example explanation:
```

To call a signed endpoint:

1. Add query parameters in alphabetical order
2. Compute HMAC-SHA256 of the query string with your secret key
3. Append signature to the query string
4. Send API-Key header

```

---

## 📌 **LANGUAGE SUPPORT**

The agent should be able to produce:

✔ Python examples
✔ JavaScript / Node.js examples
✔ cURL examples

Example output patterns should include multiple languages like:
```

Python:

...

JavaScript (Node Fetch/axios):

...

cURL:

```

---

## 🔎 **SEARCH & NAVIGATION BEHAVIOR**

The agent must be able to:

• Accept queries like:
  – “Show all Futures endpoints involving positions”
  – “What’s the Spot endpoint for order book data?”
  – “How do I handle Binance API errors?”

• Provide:
  – Summaries of sections
  – Full endpoint lists
  – Clarification on parameter semantics
  – Cross-links to relevant docs

---

## 🧪 **TEST QUESTIONS YOU SHOULD ANSWER**

These serve as internal QA:

1. “How do I place a limit order on Binance Spot?”
2. “What’s the difference between USDT-margined and COIN-margined futures?”
3. “What headers are required for signed REST calls?”
4. “Show how to get position risk for a futures account (Python).”
5. “Explain Binance rate limit rules.”

---

## 📌 **CONSTRAINTS & BEST PRACTICES**

• Always use official documentation as the primary source.
• Validate code examples with canonical endpoint definitions.
• Mention API rate limits or errors where relevant.
• Include security notes (never include secret keys in examples).

---

End prompt.
```

---