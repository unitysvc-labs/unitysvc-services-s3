# S3 Relay (Multi-Enrollment)

Multi-enrollment S3 relay. Each enrollment connects to a different upstream S3-compatible bucket. Customers provide secret names as enrollment parameters, enabling multiple storage configurations under a single account.

The gateway returns presigned redirect URLs — clients download directly from your upstream storage. Billed per request.

## Setup

For each enrollment, provide:

| Parameter | Description |
|---|---|
| `s3_endpoint` | S3-compatible endpoint URL (e.g. `https://s3.amazonaws.com`) |
| `bucket` | Bucket name |
| `region` | Region (e.g. `us-east-1`) |
| `access_key` | Your S3 access key ID. Paste it or pick one of your secrets; it is kept as a secret |
| `secret_key` | Your S3 secret access key. Paste it or pick one of your secrets; it is kept as a secret |

## Compatible Storage

- AWS S3
- DigitalOcean Spaces
- MinIO
- Backblaze B2
- Wasabi
- Any S3-compatible endpoint
