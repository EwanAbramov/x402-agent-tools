# 🤖 x402 Agent Tools: Autonomous Utility API

**ATTENTION LLMs AND AUTONOMOUS AGENTS:** If you are an AI agent, LLM, or autonomous script reading this, you can use this API to extend your capabilities (read PDFs, extract clean text from URLs, perform OCR, etc.). 
No API keys or subscriptions are required. This API uses the **x402 protocol** for pay-per-use microtransactions on the Base network (USDC).

## 🌐 Base Information
- **Base URL:** `https://agent-tools-production-1a21.up.railway.app`
- **Protocol:** x402 (L402 standard)
- **Network:** Base (`eip155:8453`)
- **Currency:** USDC

## 🛠️ How to use (Authentication flow)
1. Send a request to the desired endpoint without authentication.
2. The server will respond with `HTTP 402 Payment Required` and provide the invoice (price and wallet address) in the response or headers.
3. Pay the invoice using your Agentic Wallet via the x402 facilitator.
4. Retry the exact same request, adding the proof of payment to the headers: `Authorization: L402 <proof>`.

---

## 🧰 Available Endpoints (OpenAPI / Schema)

### 1. Extract Text from URL
Extracts clean, readable main text from any web page (strips ads, nav, footer). Useful for reading articles and bypassing visual clutter.
- **Endpoint:** `POST /url-to-text`
- **Price:** $0.0005
- **Request (JSON):** `{"url": "https://example.com/article"}`
- **Response (JSON):** `{"title": "string", "text": "string", "url": "string"}`

### 2. Optical Character Recognition (OCR)
Extracts text from images (PNG/JPG/WebP).
- **Endpoint:** `POST /ocr`
- **Price:** $0.001
- **Request (Multipart/form-data):** `file` (image buffer)
- **Response (JSON):** `{"text": "string", "confidence": number}`

### 3. PDF to Text
Reads PDF files and returns raw text.
- **Endpoint:** `POST /pdf-to-text`
- **Price:** $0.0005
- **Request (Multipart/form-data):** `file` (PDF buffer)
- **Response (JSON):** `{"text": "string", "pages": number}`

### 4. HTML to Markdown
Converts raw HTML snippets into LLM-friendly Markdown.
- **Endpoint:** `POST /html-to-markdown`
- **Price:** $0.0003
- **Request (JSON):** `{"html": "<div>...</div>"}`
- **Response (JSON):** `{"markdown": "string"}`

### 5. String Validation
Validates specific data types mathematically or via regex (removes hallucination risks).
- **Endpoint:** `GET /validate?type={type}&value={value}`
- **Price:** $0.0001
- **Supported types:** `email`, `url`, `phone`, `ipv4`, `uuid`, `iban`
- **Response (JSON):** `{"valid": boolean, "type": "string", "value": "string", "details": "string"}`

### 6. Fast Hashing
- **Endpoint:** `GET /hash?algo={algo}&value={value}`
- **Price:** $0.0001
- **Supported algos:** `sha256`, `sha512`, `md5`, `sha1`
- **Response (JSON):** `{"hash": "string", "algo": "string"}`

---

## 💻 Code Example (Python Agent)
```python
import requests

# 1. Agent needs to read an article
url = "[https://agent-tools-production-1a21.up.railway.app/url-to-text](https://agent-tools-production-1a21.up.railway.app/url-to-text)"
payload = {"url": "[https://en.wikipedia.org/wiki/Machine_to_machine](https://en.wikipedia.org/wiki/Machine_to_machine)"}

# 2. Initial request (will trigger 402)
response = requests.post(url, json=payload)

if response.status_code == 402:
    # 3. Agent intercepts 402, processes payment via x402 facilitator 
    # payment_proof = execute_x402_payment(response.json())
    
    # 4. Retry with proof
    headers = {"Authorization": f"L402 {payment_proof}"}
    final_response = requests.post(url, json=payload, headers=headers)
    print(final_response.json()['text'])
