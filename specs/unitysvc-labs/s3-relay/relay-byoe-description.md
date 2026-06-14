# S3 Relay (Bring Your Own Bucket)

Connect your own S3-compatible storage through the UnitySVC gateway. Customers store their credentials as platform secrets; the gateway relays S3 requests to your upstream bucket and returns presigned redirect URLs to clients.

## Setup

Store the following secrets in your account:

| Secret | Description |
|---|---|
| `S3_RELAY_ENDPOINT` | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com`) |
| `S3_RELAY_BUCKET` | Bucket name |
| `S3_RELAY_REGION` | Region (e.g. `us-east-1`) |
| `S3_RELAY_ACCESS_KEY` | Access key ID |
| `S3_RELAY_SECRET_KEY` | Secret access key |

## Compatible Storage

- AWS S3
- DigitalOcean Spaces
- MinIO
- Backblaze B2
- Wasabi
- Any S3-compatible endpoint
