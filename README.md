# cloudlaunch-aws-assessment

## Task 1: Static Website Hosting Using S3 + IAM
- Created 3 S3 buckets:
  - **cloudlaunch-site-bucket17** → hosts the static website (index.html, styles.css, etc.).
  - **cloudlaunch-private-bucket17** → private bucket, only accessible via IAM user with GetObject/PutObject permissions.
  - **cloudlaunch-visible-only-bucket17** → bucket that can be listed but objects are not accessible.
- Enabled **static website hosting** on `cloudlaunch-site-bucket17`.
- Attached a **bucket policy** to allow public `GetObject` access.
- Created IAM user `cloudlaunch-user` with custom JSON policy.
- Restricted permissions:
  - Can only list all three buckets.
  - Can GetObject/PutObject in `cloudlaunch-private-bucket17`.
  - Can GetObject in `cloudlaunch-site-bucket17`.
  - Cannot delete objects.
  - Cannot read `cloudlaunch-visible-only-bucket17` contents.

### Static Website Link
👉 http://cloudlaunch-site-bucket17.s3-website-eu-west-1.amazonaws.com 

---

## Task 2: VPC Design
- Created VPC `cloudlaunch17-vpc` with CIDR `10.0.0.0/16`.
- Subnets:
  - **10.0.1.0/24** → Public subnet17 (for future load balancer).
  - **10.0.2.0/24** → Application subnet17 (private).
  - **10.0.3.0/28** → Database subnet17 (private).
- Internet Gateway: `cloudlaunch17-igw` attached to the VPC.
- Route Tables:
  - `cloudlaunch-public-rt17` → routes `0.0.0.0/0` to IGW, associated with public subnet.
  - `cloudlaunch-app-rt17` → private, no internet route.
  - `cloudlaunch-db-rt17` → private, no internet route.
- Security Groups:
  - `cloudlaunch-app-sg17`: allows HTTP (80) only within VPC.
  - `cloudlaunch-db-sg17`: allows MySQL (3306) only from app subnet.

---

## IAM JSON Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::cloudlaunch-site-bucket17",
        "arn:aws:s3:::cloudlaunch-private-bucket17",
        "arn:aws:s3:::cloudlaunch-visible-only-bucket17"
      ]
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::cloudlaunch-site-bucket17/*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject","s3:PutObject"],
      "Resource": "arn:aws:s3:::cloudlaunch-private-bucket17/*"
    }
  ]
}
