# S3 Proxy (Bring Your Own Bucket)

Serve objects from your own S3-compatible storage through the UnitySVC gateway. Unlike **S3 Relay** (which returns presigned redirects), S3 Proxy **streams all bytes through the gateway** — your upstream endpoint is never exposed to clients. Use this when you need confidentiality (upstream URL hidden) or metered content delivery.

## Two ways to use this service

This service exposes two **upstream access channels**. Pick whichever fits — you can use both. The only thing that changes is **where the gateway gets your bucket, endpoint, and credentials from** — stored customer secrets, or per-enrollment parameters.

| | `byok` — one stored bucket | `plus` — per-enrollment buckets |
|---|---|---|
| Best for | one fixed bucket | many buckets under one account |
| Config source | `S3_RELAY_*` customer secrets | `bucket` / `region` / `s3_endpoint` params + named credential secrets, per enrollment |
| Reached at | the canonical gateway address (`…/s3-proxy`) | a unique `/e/<code>` address per enrollment |
| Price | **free** | **$0.01 / GB transferred** |

In both channels you authenticate to the gateway with your svcpass (`UNITYSVC_API_KEY`).

### Method 1 — Stored bucket (`byok`, free)

One bucket, configured once via customer secrets. The free proxy channel reads the **same `S3_RELAY_*` secrets as S3 Relay** — configure your bucket once and use it in redirect mode (relay) or proxy mode (proxy):

| Secret | Description |
|---|---|
| `S3_RELAY_ENDPOINT` | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com`) |
| `S3_RELAY_BUCKET` | Bucket name |
| `S3_RELAY_REGION` | Region (e.g. `us-east-1`) |
| `S3_RELAY_ACCESS_KEY` | Access key ID |
| `S3_RELAY_SECRET_KEY` | Secret access key |

Then send S3 requests to the canonical gateway address for `s3-proxy`, authenticating with your svcpass. The gateway fetches from your upstream and streams the bytes back — clients never see the upstream URL.

### Method 2 — Per-enrollment buckets (`plus`, metered)

Run **multiple** proxies under one account — each enrollment routes to a different bucket. The bucket / region / endpoint are set **directly** as enrollment parameters; the credentials are customer secrets whose *names* you pass, so you can rotate a key without re-enrolling.

For each enrollment, provide:

| Parameter | Description |
|---|---|
| `s3_endpoint` | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com`) |
| `bucket` | Bucket name |
| `region` | Region (e.g. `us-east-1`) |
| `access_key_secret` | **Name** of the customer secret holding your S3 access key ID |
| `secret_key_secret` | **Name** of the customer secret holding your S3 secret access key |

Each enrollment returns a 6-character code; send requests to that enrollment's unique `/e/<code>` gateway address.

## Compatible storage

AWS S3 · DigitalOcean Spaces · MinIO · Backblaze B2 · Wasabi · any S3-compatible endpoint.

## Proxy vs relay

Proxy streams bytes through the gateway (upstream hidden, billed per GB). If you'd rather have clients download directly from your bucket via presigned redirects (cheaper, upstream URL visible), use the **S3 Relay** service (`proxy_mode: redirect`, billed per request) instead.
