# AWS CLI Complete Setup & $100 Credit Tasks Guide

A complete, beginner-friendly guide to installing the AWS CLI, configuring authentication, executing the core onboarding tasks to claim promotional credits, and cleaning up resources to maintain **₹0 / $0** usage.

---

## Table of Contents
1. [Installing AWS CLI on macOS](#1-installing-aws-cli-on-macos)
2. [Generating AWS Access Keys](#2-generating-aws-access-keys)
3. [Configuring AWS CLI](#3-configuring-aws-cli)
4. [Executing Onboarding / Reward Tasks via CLI](#4-executing-onboarding--reward-tasks-via-cli)
   - [Task 1: IAM Security Setup (Admin Group & User)](#task-1-iam-security-setup-admin-group--user)
   - [Task 2: S3 Cloud Storage (Bucket & File Upload)](#task-2-s3-cloud-storage-bucket--file-upload)
   - [Task 3: Serverless Compute (AWS Lambda)](#task-3-serverless-compute-aws-lambda)
   - [Task 4: Compute Server (EC2 Free-Tier Instance)](#task-4-compute-server-ec2-free-tier-instance)
   - [Task 5: Cost Management (Monthly Budget Alert)](#task-5-cost-management-monthly-budget-alert)
   - [Task 6: Auditing & Governance (AWS CloudTrail)](#task-6-auditing--governance-aws-cloudtrail)
5. [Zero-Cost Cleanup (Keep Account at ₹0)](#5-zero-cost-cleanup-keep-account-at-0)
6. [Direct Dashboard URLs for Credits & Verification](#6-direct-dashboard-urls-for-credits--verification)

---

## 1. Installing AWS CLI on macOS

### Option A: Using Homebrew (Recommended)
```bash
brew install awscli
```

### Option B: Using Official macOS Installer
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
rm AWSCLIV2.pkg
```

### Verify Installation
```bash
aws --version
```
*Expected output: `aws-cli/2.x.x ...`*

---

## 2. Generating AWS Access Keys

1. Log into the [AWS Management Console](https://console.aws.amazon.com/).
2. Click your **Account Name / Username** in the top-right corner $\rightarrow$ select **Security credentials**.
3. Scroll down to the **Access keys** section.
4. Click **Create access key** $\rightarrow$ choose **Command Line Interface (CLI)**.
5. Check the confirmation checkbox $\rightarrow$ Click **Next** $\rightarrow$ Click **Create access key**.
6. Note down your:
   - **Access Key ID** (e.g. `AKIAIOSFODNN7EXAMPLE`)
   - **Secret Access Key** (e.g. `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`)

---

## 3. Configuring AWS CLI

### Method 1: Interactive Prompt
Run:
```bash
aws configure
```
Enter your details when prompted:
```text
AWS Access Key ID [None]: YOUR_ACCESS_KEY_ID
AWS Secret Access Key [None]: YOUR_SECRET_ACCESS_KEY
Default region name [None]: us-east-1
Default output format [None]: json
```

### Method 2: Direct One-Line Setup
```bash
aws configure set aws_access_key_id "YOUR_ACCESS_KEY_ID"
aws configure set aws_secret_access_key "YOUR_SECRET_ACCESS_KEY"
aws configure set default.region "us-east-1"
aws configure set default.output "json"
```

### Verify Authentication
```bash
aws sts get-caller-identity
```
*Expected output:*
```json
{
    "UserId": "151105438571",
    "Account": "151105438571",
    "Arn": "arn:aws:iam::151105438571:root"
}
```

---

## 4. Executing Onboarding / Reward Tasks via CLI

Replace `YOUR_ACCOUNT_ID` with your 12-digit AWS Account ID (e.g. `151105438571`).

---

### Task 1: IAM Security Setup (Admin Group & User)
Creates an administrator group and dedicated admin user so you follow security best practices.

```bash
# 1. Create the Admins group
aws iam create-group --group-name Admins

# 2. Attach AdministratorAccess policy to group
aws iam attach-group-policy \
  --group-name Admins \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# 3. Create a new IAM User
aws iam create-user --user-name admin-developer

# 4. Add user to the Admins group
aws iam add-user-to-group --group-name Admins --user-name admin-developer
```

---

### Task 2: S3 Cloud Storage (Bucket & File Upload)
Creates an object storage bucket and uploads a test file.

```bash
# 1. Create S3 Bucket (bucket names must be globally unique)
aws s3api create-bucket \
  --bucket cloud-onboarding-YOUR_ACCOUNT_ID \
  --region us-east-1

# 2. Upload a sample file
echo "AWS Onboarding Completed Successfully" > /tmp/welcome.txt
aws s3 cp /tmp/welcome.txt s3://cloud-onboarding-YOUR_ACCOUNT_ID/welcome.txt
```

---

### Task 3: Serverless Compute (AWS Lambda)
Creates an execution role, deploys a Node.js Lambda function, and triggers an execution test.

```bash
# 1. Create Trust Policy file
cat << 'EOF' > /tmp/trust-policy.json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

# 2. Create sample JavaScript handler code
cat << 'EOF' > /tmp/index.js
exports.handler = async (event) => {
    return {
        statusCode: 200,
        body: JSON.stringify({ message: "AWS Reward Task Completed Successfully!" })
    };
};
EOF

# 3. Package code into ZIP
cd /tmp && zip -q function.zip index.js

# 4. Create IAM Role for Lambda
aws iam create-role \
  --role-name LambdaBasicRole-Onboarding \
  --assume-role-policy-document file:///tmp/trust-policy.json

# 5. Attach Basic Execution Policy
aws iam attach-role-policy \
  --role-name LambdaBasicRole-Onboarding \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

# 6. Wait 5 seconds for IAM propagation, then create Lambda function
sleep 5
aws lambda create-function \
  --function-name OnboardingTaskLambda \
  --runtime nodejs18.x \
  --role arn:aws:iam::YOUR_ACCOUNT_ID:role/LambdaBasicRole-Onboarding \
  --handler index.handler \
  --zip-file fileb:///tmp/function.zip \
  --region us-east-1

# 7. Invoke the function to record active execution
sleep 5
aws lambda invoke \
  --function-name OnboardingTaskLambda \
  --region us-east-1 \
  /tmp/lambda-output.json
```

---

### Task 4: Compute Server (EC2 Free-Tier Instance)
Launches a free-tier eligible `t3.micro` Amazon Linux 2023 instance.

```bash
# 1. Fetch latest Amazon Linux 2023 AMI ID
AMI_ID=$(aws ssm get-parameter \
  --name /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 \
  --region us-east-1 \
  --query "Parameter.Value" \
  --output text)

# 2. Launch instance
aws ec2 run-instances \
  --image-id "$AMI_ID" \
  --instance-type t3.micro \
  --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=Onboarding-EC2-Demo}]' \
  --region us-east-1
```

---

### Task 5: Cost Management (Monthly Budget Alert)
Creates an automated billing alert if projected or actual cost reaches $10 USD.

```bash
# 1. Create budget definition JSON
cat << 'EOF' > /tmp/budget.json
{
    "BudgetName": "MonthlyCostBudget-10USD",
    "BudgetLimit": {
        "Amount": "10.0",
        "Unit": "USD"
    },
    "CostTypes": {
        "IncludeTax": true,
        "IncludeSubscription": true,
        "UseBlended": false,
        "IncludeRefund": false,
        "IncludeCredit": false,
        "IncludeUpfront": true,
        "IncludeRecurring": true,
        "IncludeOtherSubscription": true,
        "IncludeSupport": true,
        "IncludeDiscount": true,
        "UseAmortized": false
    },
    "TimeUnit": "MONTHLY",
    "BudgetType": "COST"
}
EOF

# 2. Deploy the Budget
aws budgets create-budget \
  --account-id YOUR_ACCOUNT_ID \
  --budget file:///tmp/budget.json
```

---

### Task 6: Auditing & Governance (AWS CloudTrail)
Enables auditing and governance logging for API calls across your account.

```bash
# 1. Create a dedicated S3 logging bucket
aws s3api create-bucket \
  --bucket cloudtrail-audit-YOUR_ACCOUNT_ID \
  --region us-east-1

# 2. Apply CloudTrail permissions policy to bucket
cat << EOF > /tmp/cloudtrail-policy.json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSCloudTrailAclCheck",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:GetBucketAcl",
            "Resource": "arn:aws:s3:::cloudtrail-audit-YOUR_ACCOUNT_ID"
        },
        {
            "Sid": "AWSCloudTrailWrite",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudtrail.amazonaws.com"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::cloudtrail-audit-YOUR_ACCOUNT_ID/AWSLogs/YOUR_ACCOUNT_ID/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": "bucket-owner-full-control"
                }
            }
        }
    ]
}
EOF

aws s3api put-bucket-policy \
  --bucket cloudtrail-audit-YOUR_ACCOUNT_ID \
  --policy file:///tmp/cloudtrail-policy.json

# 3. Create and start the trail
aws cloudtrail create-trail \
  --name AccountOnboardingTrail \
  --s3-bucket-name cloudtrail-audit-YOUR_ACCOUNT_ID \
  --region us-east-1

aws cloudtrail start-logging \
  --name AccountOnboardingTrail \
  --region us-east-1
```

---

## 5. Zero-Cost Cleanup (Keep Account at ₹0)

After your tasks are marked as completed on your dashboard, run these commands to shut down any active resources so you incur zero charges:

```bash
# 1. Terminate all running demo EC2 instances
INSTANCE_IDS=$(aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=Onboarding-EC2-Demo" "Name=instance-state-name,Values=running,pending" \
  --query "Reservations[*].Instances[*].InstanceId" \
  --output text \
  --region us-east-1)

if [ -n "$INSTANCE_IDS" ]; then
  aws ec2 terminate-instances --instance-ids $INSTANCE_IDS --region us-east-1
fi

# 2. Stop CloudTrail logging (prevents ongoing S3 PUT calls)
aws cloudtrail stop-logging --name AccountOnboardingTrail --region us-east-1 2>/dev/null || true
```

---

## 6. Direct Dashboard URLs for Credits & Verification

| Dashboard | Direct URL Link | Purpose |
|---|---|---|
| **Promotional Credits** | [Billing Credits Page](https://console.aws.amazon.com/billing/home#/credits) | Check your $100 credit balance or redeem promo codes |
| **Console Home & Checklist** | [Console Home (us-east-1)](https://console.aws.amazon.com/console/home?region=us-east-1) | View the Getting Started / Onboarding task checklist |
| **AWS Activate Portal** | [AWS Activate Dashboard](https://console.aws.amazon.com/activate/home) | Startup & Founder credit programs |
| **Free Tier Usage** | [Free Tier Dashboard](https://console.aws.amazon.com/billing/home#/freetier) | Track remaining free-tier allowance |
| **AWS Budgets** | [Budgets Dashboard](https://console.aws.amazon.com/billing/home#/budgets) | View and manage monthly cost thresholds |
