# UnitySVC Services - S3

This repository hosts S3 service data for the UnitySVC platform, provided by **UnitySVC Labs**.

## Overview

These services let customers serve objects from their **own** S3-compatible storage
(AWS S3, DigitalOcean Spaces, MinIO, Backblaze B2, Wasabi, or any S3-compatible endpoint)
through the UnitySVC S3 gateway (`s3.unitysvc.com`). Customers bring the bucket and
credentials; the gateway handles auth, routing, and metering. No files are self-hosted.

## Services

Each service is **multi-channel**: a free `byok` channel (one bucket configured once via
`S3_RELAY_*` customer secrets) and a metered `plus` channel (per-enrollment buckets,
reached at `/e/<code>`).

| Service | Delivery mode | `byok` (free) | `plus` (metered) |
|---|---|---|---|
| `s3-relay` | `redirect` — gateway returns presigned redirect URLs; clients download directly from your bucket | $0 | $0.001 / request |
| `s3-proxy` | `proxy` — gateway streams all bytes; upstream endpoint stays hidden from clients | $0 | $0.01 / GB transferred |

The `byok` proxy channel reads the same `S3_RELAY_*` secrets as `s3-relay`, so a single
bucket configuration works in either redirect or proxy mode.

## Usage

After configuring your bucket (or enrolling), use any S3-compatible client against the
gateway, authenticating with your svcpass API key:

```python
import boto3

s3 = boto3.client('s3',
    endpoint_url='https://s3.unitysvc.com',
    aws_access_key_id='svcpass_your_api_key',
    aws_secret_access_key='not-used',
)

response = s3.list_objects_v2(Bucket='s3-relay', MaxKeys=10)
for obj in response.get('Contents', []):
    print(f"{obj['Key']}  ({obj['Size']} bytes)")
```

See each service's "How to use this service" document for the full `byok` / `plus` setup.

## Setup

```bash
pip install unitysvc-services
usvc data validate
```

## Related

- [#531](https://github.com/unitysvc/unitysvc/issues/531) — S3-compatible storage gateway
- [unitysvc-services](https://github.com/unitysvc/unitysvc-services) — Seller SDK
