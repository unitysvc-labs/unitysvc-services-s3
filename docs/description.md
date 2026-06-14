## Public S3 Datasets via UnitySVC Storage Gateway

UnitySVC Labs provides free access to curated public datasets through the S3-compatible storage gateway at `s3.unitysvc.com`.

### How to use

Use any S3-compatible client (boto3, aws-cli, rclone, cyberduck) with your UnitySVC API key:

```python
import boto3

s3 = boto3.client('s3',
    endpoint_url='{{ S3_GATEWAY_PUBLIC_URL }}',
    aws_access_key_id='{{ API_KEY }}',
    aws_secret_access_key='not-used',
)

# Browse available files
s3.list_objects_v2(Bucket='{{ SERVICE_NAME }}', MaxKeys=10)
```

Or with the AWS CLI:

```bash
aws --endpoint-url {{ S3_GATEWAY_PUBLIC_URL }} s3 ls s3://{{ SERVICE_NAME }}/
```

### About these services

These datasets are hosted on public S3 buckets (AWS Open Data Registry and similar programs). UnitySVC Labs proxies access through the storage gateway, providing:

- **Unified access** with your UnitySVC API key
- **Usage tracking** for your downloads
- **Curated catalog** instead of hunting for bucket names and regions

The data is free. The gateway infrastructure is the service.
