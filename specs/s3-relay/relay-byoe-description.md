# S3 Relay (Bring Your Own Bucket)

Serve objects from your own S3-compatible storage through the UnitySVC gateway. The gateway authenticates your call with your svcpass key, resolves which upstream bucket to reach, and returns **presigned redirect URLs** — clients then download directly from your upstream storage, so the gateway never moves the bytes (and takes no commission on them).

## Two ways to use this service

This service exposes two **upstream access channels**. Pick whichever fits — you can use both. The only thing that changes is **where the gateway gets your bucket, endpoint, and credentials from** — stored customer secrets, or per-enrollment parameters.

| | `byok` — one stored bucket | `plus` — per-enrollment buckets |
|---|---|---|
| Best for | one fixed bucket | many buckets under one account |
| Config source | `S3_RELAY_*` customer secrets | `bucket` / `region` / `s3_endpoint` params + named credential secrets, per enrollment |
| Reached at | the canonical gateway address (`…/s3-relay`) | a unique `/e/<code>` address per enrollment |
| Price | **free** | **$0.001 / request relayed** |

In both channels you authenticate to the gateway with your svcpass (`UNITYSVC_API_KEY`).

### Method 1 — Stored bucket (`byok`, free)

One bucket, configured once via customer secrets:

| Secret | Description |
|---|---|
| `S3_RELAY_ENDPOINT` | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com`) |
| `S3_RELAY_BUCKET` | Bucket name |
| `S3_RELAY_REGION` | Region (e.g. `us-east-1`) |
| `S3_RELAY_ACCESS_KEY` | Access key ID |
| `S3_RELAY_SECRET_KEY` | Secret access key |

Then send S3 requests to the canonical gateway address for `s3-relay`, authenticating with your svcpass.

### Method 2 — Per-enrollment buckets (`plus`, metered)

Run **multiple** relays under one account — each enrollment routes to a different bucket. The bucket / region / endpoint are set **directly** as enrollment parameters; the credentials are customer secrets whose *names* you pass, so you can rotate a key without re-enrolling.

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

## Relay vs proxy

Relay returns presigned redirects (cheap, fast — clients hit your bucket directly, so the upstream URL is visible to them). If you need the upstream endpoint **hidden** and the bytes streamed through the gateway, use the **S3 Proxy** service (`proxy_mode: proxy`, billed per GB) instead.
