# NovaPay Webhooks (Sample Page)

> Sample documentation file added to test Promptless. It intentionally contains details that may be out of date.

## Overview

NovaPay sends webhook events to your server when something happens on your account, such as a payment succeeding or a refund being issued.

## Setup

1. Sign in to the NovaPay dashboard and open **Settings > Developers > Webhooks**.
2. Click **Add endpoint** and enter your HTTPS URL.
3. Select the events you want to receive.
4. Save. NovaPay shows a signing secret that starts with `whsec_`.

## Supported events

| Event | Description |
| --- | --- |
| `payment.succeeded` | A payment was captured. |
| `payment.failed` | A payment attempt failed. |
| `refund.created` | A refund was issued. |
| `payout.paid` | A payout reached your bank account. |

## Verifying signatures

Every request includes an `X-NovaPay-Signature` header containing an HMAC-SHA1 of the raw request body, computed with your signing secret. Compare it to your own computation before trusting the payload.

## Retries

If your endpoint does not return a `2xx` status within 5 seconds, NovaPay retries the delivery up to 3 times, at 1 minute, 5 minutes and 30 minutes after the first attempt.

## API version

Webhook payloads follow API version `v1`. The `v1` API is supported until December 2025.

## Requirements

- An HTTPS endpoint with TLS 1.0 or later.
- The NovaPay Python SDK 1.x (requires Python 3.7+).
