# AWS CLI Complete Setup & $100 Credit Tasks Guide

A complete, beginner-friendly guide to installing the AWS CLI, configuring authentication, executing the exact **5 onboarding activities** to earn the **US$100 AWS credits**, and cleaning up resources to maintain **₹0 / $0** usage.

---

## 🎯 The 5 AWS Credit Reward Activities ($100 Total)

| Activity | Reward | Status | CLI Command / Action |
|---|---|---|---|
| **1. Create an Aurora or RDS database** | **US$20** | ✅ Completed | `aws rds create-db-instance` |
| **2. Launch an instance using EC2** | **US$20** | ✅ Completed | `aws ec2 run-instances` |
| **3. Set up a cost budget using AWS Budgets** | **US$20** | ✅ Completed | `aws budgets create-budget` |
| **4. Create a web app using AWS Lambda** | **US$20** | ✅ Completed | `aws lambda create-function` |
| **5. Use a foundation model in Amazon Bedrock** | **US$20** | ✅ Completed | `aws bedrock-runtime invoke-model` |

---

## Table of Contents
1. [Installing AWS CLI on macOS](#1-installing-aws-cli-on-macos)
2. [Generating AWS Access Keys](#2-generating-aws-access-keys)
3. [Configuring AWS CLI](#3-configuring-aws-cli)
4. [Step-by-Step Execution of the 5 Reward Activities](#4-step-by-step-execution-of-the-5-reward-activities)
   - [Activity 1: Create an Aurora or RDS database ($20)](#activity-1-create-an-aurora-or-rds-database-20)
   - [Activity 2: Launch an instance using EC2 ($20)](#activity-2-launch-an-instance-using-ec2-20)
   - [Activity 3: Set up a cost budget using AWS Budgets ($20)](#activity-3-set-up-a-cost-budget-using-aws-budgets-20)
   - [Activity 4: Create a web app using AWS Lambda ($20)](#activity-4-create-a-web-app-using-aws-lambda-20)
   - [Activity 5: Use a foundation model in Amazon Bedrock ($20)](#activity-5-use-a-foundation-model-in-amazon-bedrock-20)
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

### Direct One-Line Setup
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

## 4. Step-by-Step Execution of the 5 Reward Activities

---

### Activity 1: Create an Aurora or RDS database ($20)
Creates a lightweight PostgreSQL database instance in RDS.

```bash
aws rds create-db-instance \
  --db-instance-identifier onboarding-demo-db \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --master-username dbadmin \
  --master-user-password "StrongPass123Onboarding!" \
  --allocated-storage 20 \
  --no-publicly-accessible \
  --region us-east-1
```

---

### Activity 2: Launch an instance using EC2 ($20)
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

### Activity 3: Set up a cost budget using AWS Budgets ($20)
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
  --account-id 151105438571 \
  --budget file:///tmp/budget.json
```

---

### Activity 4: Create a web app using AWS Lambda ($20)
Creates an execution role, deploys a Node.js Lambda function, and invokes it.

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

# 2. Create handler code
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
  --assume-role-policy-document file:///tmp/trust-policy.json 2>/dev/null || true

aws iam attach-role-policy \
  --role-name LambdaBasicRole-Onboarding \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole 2>/dev/null || true

# 5. Create Lambda function
sleep 5
aws lambda create-function \
  --function-name OnboardingTaskLambda \
  --runtime nodejs18.x \
  --role arn:aws:iam::151105438571:role/LambdaBasicRole-Onboarding \
  --handler index.handler \
  --zip-file fileb:///tmp/function.zip \
  --region us-east-1

# 6. Invoke function
sleep 5
aws lambda invoke \
  --function-name OnboardingTaskLambda \
  --region us-east-1 \
  /tmp/lambda-output.json
```

---

### Activity 5: Use a foundation model in Amazon Bedrock ($20)
Invokes Amazon Bedrock foundation model (e.g. Amazon Titan / Claude).

```bash
aws bedrock-runtime invoke-model \
  --model-id amazon.titan-text-express-v1 \
  --body '{"inputText":"Hello Amazon Bedrock"}' \
  --region us-east-1 \
  /tmp/bedrock-output.json 2>/dev/null || true
```
*(Or by simply opening the Amazon Bedrock Console $\rightarrow$ **Playgrounds** $\rightarrow$ **Chat / Text** and sending a prompt).*

---

## 5. Zero-Cost Cleanup (Keep Account at ₹0)

After the tasks register as completed on your dashboard, clean up all active instances to avoid any charges:

```bash
# 1. Delete RDS Database instance
aws rds delete-db-instance \
  --db-instance-identifier onboarding-demo-db \
  --skip-final-snapshot \
  --delete-automated-backups \
  --region us-east-1 2>/dev/null || true

# 2. Terminate demo EC2 instance
INSTANCE_IDS=$(aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=Onboarding-EC2-Demo" "Name=instance-state-name,Values=running,pending" \
  --query "Reservations[*].Instances[*].InstanceId" \
  --output text \
  --region us-east-1)

if [ -n "$INSTANCE_IDS" ]; then
  aws ec2 terminate-instances --instance-ids $INSTANCE_IDS --region us-east-1
fi
```

---

## 6. Direct Dashboard URLs for Credits & Verification

| Dashboard | Direct URL Link | Purpose |
|---|---|---|
| **AWS Explore / Earn Credits** | [Console Home (us-east-1)](https://console.aws.amazon.com/console/home?region=us-east-1) | Check off the 5 completed activities |
| **Promotional Credits Balance** | [Billing Credits Page](https://console.aws.amazon.com/billing/home#/credits) | View your total **US$100** credit balance |
| **Free Tier Usage** | [Free Tier Dashboard](https://console.aws.amazon.com/billing/home#/freetier) | Confirm ₹0 / $0 monthly consumption |
| **AWS Budgets** | [Budgets Dashboard](https://console.aws.amazon.com/billing/home#/budgets) | Manage cost threshold alerts |
