# KBank Payment Test UI (moved)

> **This repository has moved into villaPayment3.**
> It now lives at [`subrepos/payment3-test-ui`](https://github.com/villa-market/villaPayment3/tree/master/subrepos/payment3-test-ui)
> in [villa-market/villaPayment3](https://github.com/villa-market/villaPayment3), imported with its full
> history (villa-market/villaPayment3#205). Make changes there; this repository stays as a read-only archive.

Test page for verifying the Villa Payment3 backend card payment flow. Demonstrates that the backend correctly handles KBank payment requests and returns proper redirects.

## Quick Start

```bash
# Serve locally (any HTTP server works)
python3 -m http.server 8080

# Open in browser
open http://localhost:8080
```

Or just open `index.html` directly in your browser — most features work via `file://` too.

## What This Tests

### Test Backend Redirect (No KBank SDK)
Click **"Test Backend Redirect"** to submit a form POST directly to the backend with an invalid token. The backend should:
1. Accept the POST (HTTP 200, not 400/500)
2. Return HTML with a meta redirect
3. Redirect to your callback URL with `?status=failure&orderId=...&amount=...`

This proves the backend redirect fix is working — failures return a proper redirect instead of an error page.

### Load KBank Payment Form (Full SDK)
Click **"Load KBank Payment Form"** to load the KBank SDK and open the native payment modal. Enter test card details to complete a full payment flow.

## Test Credentials

| Field | Value |
|-------|-------|
| Card Number | `5444880117276129` (Mastercard) |
| Expiry | Any future date (e.g., `12/25`) |
| CVV | Any 3 digits (e.g., `123`) |
| OTP | `123456` |

## Configuration

The test page has configurable fields:
- **Callback URL** — Where to redirect after payment (default: `https://httpbin.org/get` which displays params as JSON)
- **Order ID** — Test order identifier
- **Amount (THB)** — Payment amount
- **bypass_order** — Skip order verification (checked by default for testing)

## API Endpoint

```
POST https://shop.villamarket.com/api/payment3devapi/cardpayment
```

## Payment Flow

```
[This Page] → POST /cardpayment → [Backend validates + calls KBank]
    ↓
[KBank 3DS/OTP] → [KBank callback to backend]
    ↓
[Backend] → Redirect to callback URL with ?status=success|failure&orderId=...
```

## Deploy Options

**GitHub Pages:**
This repo is public — enable GitHub Pages in Settings → Pages → Source: main branch.

**Any static host:**
It's a single HTML file with no dependencies. Drop it on S3, Netlify, Vercel, or any web server.

**Local:**
```bash
python3 -m http.server 8080
# or
npx serve .
```

## Environment

- **API**: Dev (`payment3devapi`)
- **Region**: ap-southeast-1
- **KBank SDK**: v2 (Sandbox/Test mode)
- **Merchant ID**: 401394788145001
