# NovaPay webhooks (sample page)

> Sample documentation file added to test Promptless. It intentionally contains details that may be out of date.

## Overview

NovaPay sends webhook events to your server when something happens on your account, such as a payment succeeding or a refund being issued.

## Setup

1. Log in to the NovaPay dashboard and open **Settings > Developers > Webhooks**.
2. Select **Add endpoint** and enter your HTTPS URL.
3. Select the events you want to receive.
4. Save the endpoint. NovaPay shows a signing secret that starts with `whsec_`.

Treat the signing secret like a password. Store it in a secrets manager or an environment variable, and keep it out of source control.

## Supported events

| Event | Description |
| --- | --- |
| `payment.succeeded` | A payment was captured. |
| `payment.failed` | A payment attempt failed. |
| `refund.created` | A refund was issued. |
| `payout.paid` | A payout reached your bank account. |

## Verifying signatures

Every request includes an `X-NovaPay-Signature` header containing an HMAC-SHA256 of the raw request body, computed with your signing secret. Verify the signature before you trust or act on the payload:

1. Read the raw request body as bytes. If you parse and re-serialize the JSON first, the bytes can change and the signatures won't match.
2. Compute an HMAC-SHA256 of those bytes with your signing secret.
3. Compare your result to the header value with a constant-time function, such as Python's `hmac.compare_digest`. An ordinary string comparison can leak timing information to an attacker.
4. If the values don't match, reject the request and don't process the event.

## Retries

NovaPay retries a delivery if your endpoint does not return a `2xx` status within 5 seconds. It makes up to 3 retries, at 1 minute, 5 minutes, and 30 minutes after the first attempt.

To avoid timeouts, return a `2xx` status as soon as you have verified the signature, and then process the event asynchronously. Because NovaPay retries deliveries, your endpoint can receive the same event more than once. Record which events you have already processed and skip duplicates.

## API version

Webhook payloads follow API version `v1`.

## Requirements

- An HTTPS endpoint that supports TLS 1.2 or later.
- The NovaPay Python SDK 1.x, running on Python 3.11 or later.
