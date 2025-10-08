# AWS Daily Usage and Cost Report

![AWS](https://img.shields.io/badge/AWS-CloudFormation-orange)
![Python](https://img.shields.io/badge/Python-3.13-blue)
![License](https://img.shields.io/badge/License-MIT-green)

An automated AWS resource monitoring and cost reporting solution that scans your AWS infrastructure daily and sends detailed HTML email reports with approximate monthly costs.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Method 1: CloudFormation Deployment (Recommended)](#method-1-cloudformation-deployment-recommended)
  - [Method 2: Manual Local Execution](#method-2-manual-local-execution)
- [Configuration](#configuration)
- [Usage](#usage)
- [Cost Breakdown](#cost-breakdown)
- [Email Report Sample](#email-report-sample)
- [Troubleshooting](#troubleshooting)
- [Security Considerations](#security-considerations)
- [Limitations](#limitations)
- [Contributing](#contributing)
- [License](#license)

---

## 🎯 Overview

This solution automatically monitors your AWS resources across multiple regions and generates daily cost reports. It helps you:

- **Track AWS spending** in real-time with approximate monthly cost projections
- **Identify cost optimization opportunities** (unattached EBS volumes, idle resources, etc.)
- **Monitor resource inventory** across multiple AWS regions
- **Receive automated daily reports** via email

The solution uses AWS Lambda, CloudWatch Events, and Amazon SES to deliver a fully automated, serverless reporting system.

---

## ✨ Features

### Monitored Resources

- ✅ **EC2 Instances** - Running instances with instance type, OS, and cost
- ✅ **EBS Volumes** - All volumes (attached and unattached) with size, type, and cost
- ✅ **Elastic IPs & Public IPs** - All allocated IPs (including ephemeral public IPs)
- ✅ **RDS Instances** - Database instances with engine type and cost

### Key Capabilities

- 📊 **Real-time AWS Pricing** - Fetches current pricing via AWS Pricing API
- 🌍 **Multi-region Support** - Scan resources across multiple AWS regions
- 💰 **Cost Estimation** - Calculates approximate monthly costs per resource
- 📧 **HTML Email Reports** - Formatted, easy-to-read reports with tables
- ⚡ **Serverless Architecture** - No servers to manage, fully automated
- 🔄 **Daily Scheduling** - Configurable execution time (UTC)
- 💾 **Price Caching** - Optimized API calls with intelligent caching
- 🔍 **Detailed Breakdown** - Resource-level cost visibility

### Cost Calculation Features

- OS-aware pricing (Windows vs Linux)
- EBS volume type pricing (gp2, gp3, io1, io2, st1, sc1)
- RDS engine-specific pricing (MySQL, PostgreSQL, Aurora, etc.)
- Regional pricing differences
- Public IP address charges (as of 2023 pricing model)

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     AWS CloudFormation                       │
│                                                              │
│  ┌────────────────┐      ┌──────────────────┐              │
│  │  EventBridge   │─────>│  Lambda Function │              │
│  │  (Cron Rule)   │      │  (Python 3.13)   │              │
│  └────────────────┘      └────────┬─────────┘              │
│   Daily Trigger                   │                         │
│   at specified hour               │                         │
│                                   ▼                         │
│                    ┌──────────────────────────┐            │
│                    │  Scan AWS Resources:     │            │
│                    │  • EC2 Instances         │            │
│                    │  • EBS Volumes           │            │
│                    │  • Elastic IPs           │            │
│                    │  • RDS Instances         │            │
│                    └──────────┬───────────────┘            │
│                               │                             │
│                               ▼                             │
│                    ┌──────────────────────────┐            │
│                    │  AWS Pricing API         │            │
│                    │  (Get current prices)    │            │
│                    └──────────┬───────────────┘            │
│                               │                             │
│                               ▼                             │
│                    ┌──────────────────────────┐            │
│                    │  Generate HTML Report    │            │
│                    │  with cost breakdown     │            │
│                    └──────────┬───────────────┘            │
│                               │                             │
│                               ▼                             │
│                    ┌──────────────────────────┐            │
│                    │  Amazon SES              │            │
│                    │  (Send Email)            │            │
│                    └──────────────────────────┘            │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Components

1. **CloudFormation Template** (`daily-usage-report-cloudformation.template`)
   - Defines all AWS resources
   - Configurable parameters
   - Automatic resource provisioning

2. **Lambda Function** (`inventory.py`)
   - Python 3.13 runtime
   - 15-minute timeout
   - Scans multiple AWS regions
   - Calculates costs using AWS Pricing API
   - Sends formatted email reports

3. **IAM Role**
   - Least-privilege permissions
   - EC2, RDS, CloudTrail, Pricing API access
   - SES email sending permissions
   - CloudWatch Logs for monitoring

4. **EventBridge Rule**
   - Daily cron schedule
   - Triggers Lambda function
   - Configurable execution time

---

## 📋 Prerequisites

### AWS Requirements

- **AWS Account** with appropriate permissions
- **IAM Permissions** to create:
  - Lambda Functions
  - IAM Roles
  - EventBridge Rules
  - CloudFormation Stacks
- **Amazon SES** access (available in `us-east-1` or your chosen region)
- **S3 Bucket** for Lambda deployment package (if using CloudFormation)

### Required Services Access

Ensure your AWS account has access to:
- AWS Lambda
- Amazon EC2
- Amazon RDS
- Amazon SES (Simple Email Service)
- AWS Pricing API
- CloudWatch Logs
- CloudTrail (optional, for EIP creation dates)

### Email Verification

- **Sender Email**: Must be verified in Amazon SES
- **Recipient Email(s)**: Must be verified in Amazon SES (if in SES Sandbox mode)

> **Note**: If your AWS account is in **SES Sandbox mode**, both sender and receiver emails must be verified. To send to any email address, you need to [request production access](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html) for SES.

---

## 🚀 Installation

### Method 1: CloudFormation Deployment (Recommended)

This is the easiest and most automated approach.

#### Step 1: Prepare Lambda Deployment Package

1. **Create a deployment directory**:
   ```bash
   mkdir -p lambda-package
   cd lambda-package
   ```

2. **Copy the inventory.py file**:
   ```bash
   cp /path/to/inventory.py .
   ```

3. **Create deployment package**:
   ```bash
   zip -r daily-usage-report.zip inventory.py
   ```

#### Step 2: Upload to S3

1. **Create or use existing S3 bucket**:
   ```bash
   aws s3 mb s3://your-bucket-name --region us-east-1
   ```

2. **Upload the zip file**:
   ```bash
   aws s3 cp daily-usage-report.zip \
     s3://your-bucket-name/assets/cost-optimization/daily-usage-with-cost/
   ```

   Or use AWS Console:
   - Go to S3
   - Upload `daily-usage-report.zip` to the path specified above

#### Step 3: Update CloudFormation Template

Edit `daily-usage-report-cloudformation.template`:

```json
"Code": {
    "S3Bucket": "your-bucket-name",  // Change this
    "S3Key": "assets/cost-optimization/daily-usage-with-cost/daily-usage-report.zip"
}
```

#### Step 4: Deploy CloudFormation Stack

**Option A: Using AWS Console**

1. Open [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation)
2. Click **"Create Stack"** → **"With new resources"**
3. Choose **"Upload a template file"**
4. Upload `daily-usage-report-cloudformation.template`
5. Click **"Next"**
6. **Configure parameters**:
   - **Stack name**: `aws-daily-usage-report`
   - **FromEmail**: Your verified sender email
   - **ToEmail**: Recipient email(s) (comma-separated)
   - **Schedule**: Hour (0-23, UTC) for daily execution
   - **AwsRegions**: Regions to scan (e.g., `us-east-1,us-west-2,ap-south-1`)
7. Click **"Next"** → Check **"I acknowledge that AWS CloudFormation might create IAM resources"**
8. Click **"Create Stack"**
9. Wait for stack creation (Status: `CREATE_COMPLETE`)

**Option B: Using AWS CLI**

```bash
aws cloudformation create-stack \
  --stack-name aws-daily-usage-report \
  --template-body file://daily-usage-report-cloudformation.template \
  --parameters \
    ParameterKey=FromEmail,ParameterValue=sender@example.com \
    ParameterKey=ToEmail,ParameterValue=receiver@example.com \
    ParameterKey=Schedule,ParameterValue=19 \
    ParameterKey=AwsRegions,ParameterValue=us-east-1,us-west-2 \
  --capabilities CAPABILITY_IAM \
  --region us-east-1
```

#### Step 5: Verify Email Addresses

1. **Check your sender email** for verification email from AWS SES
2. Click the verification link
3. **Repeat for recipient email(s)** if in SES Sandbox mode
4. Verify in AWS Console: **SES** → **Verified Identities**

---

### Method 2: Manual Local Execution

For testing or running the script locally without Lambda.

#### Step 1: Install Prerequisites

1. **Install Python 3.8+**:
   ```bash
   python3 --version  # Should be 3.8 or higher
   ```

2. **Install AWS CLI**:
   ```bash
   # macOS
   brew install awscli
   
   # Linux
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   
   # Windows
   # Download from https://aws.amazon.com/cli/
   ```

3. **Install Boto3** (AWS SDK for Python):
   ```bash
   pip3 install boto3
   ```

#### Step 2: Configure AWS Credentials

```bash
aws configure
```

Provide:
- AWS Access Key ID
- AWS Secret Access Key
- Default region (e.g., `us-east-1`)
- Output format: `json`

Verify:
```bash
aws sts get-caller-identity
```

#### Step 3: Set Environment Variables

**Linux/macOS:**
```bash
export SENDER_EMAIL="sender@example.com"
export RECV_EMAIL="receiver1@example.com,receiver2@example.com"
export REGIONS="us-east-1,us-west-2,ap-south-1"
export SES_REGION="us-east-1"
```

**Windows (PowerShell):**
```powershell
$env:SENDER_EMAIL="sender@example.com"
$env:RECV_EMAIL="receiver1@example.com,receiver2@example.com"
$env:REGIONS="us-east-1,us-west-2,ap-south-1"
$env:SES_REGION="us-east-1"
```

#### Step 4: Verify SES Email Addresses

```bash
# Verify sender email
aws ses verify-email-identity --email-address sender@example.com

# Verify receiver email(s)
aws ses verify-email-identity --email-address receiver@example.com
```

Check your email for verification links.

#### Step 5: Run the Script

1. **Edit `inventory.py`** - Uncomment the last line:
   ```python
   # Change this line:
   #lambda_handler(None, None)
   
   # To this:
   lambda_handler(None, None)
   ```

2. **Execute**:
   ```bash
   python3 inventory.py
   ```

3. **Check output**:
   - Console will show scanning progress
   - Email will be sent upon completion
   - Check for any errors in the output

---

## ⚙️ Configuration

### CloudFormation Parameters

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `FromEmail` | Sender email address (must be verified in SES) | None | `alerts@company.com` |
| `ToEmail` | Recipient email(s), comma-separated | None | `team@company.com,admin@company.com` |
| `Schedule` | Hour of day (UTC) to run report | 19 | `19` (7 PM UTC) |
| `AwsRegions` | AWS regions to scan, comma-separated | `us-east-1` | `us-east-1,us-west-2,ap-south-1` |

### Environment Variables (Local Execution)

| Variable | Description | Required |
|----------|-------------|----------|
| `SENDER_EMAIL` | Email address to send from | Yes |
| `RECV_EMAIL` | Comma-separated recipient emails | Yes |
| `REGIONS` | Comma-separated AWS regions | Yes |
| `SES_REGION` | AWS region where SES is configured | Yes |

### Supported AWS Regions

All AWS regions are supported. Common examples:

- **US**: `us-east-1`, `us-east-2`, `us-west-1`, `us-west-2`
- **EU**: `eu-west-1`, `eu-central-1`, `eu-north-1`
- **Asia Pacific**: `ap-south-1`, `ap-southeast-1`, `ap-northeast-1`
- **Canada**: `ca-central-1`
- **South America**: `sa-east-1`

Full list: [AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande.html)

---

## 📊 Usage

### CloudFormation Deployment

Once deployed, the system runs **automatically** at the scheduled time (UTC) every day.

**Manual Trigger (for testing)**:

1. Go to **AWS Lambda Console**
2. Find function: `aws-daily-usage-report-AWSUsageLambdaFunction-XXXXX`
3. Click **"Test"**
4. Create test event (empty JSON `{}`)
5. Click **"Test"** to execute immediately

### Monitoring

**View Logs:**
```bash
# Get log group name
aws logs describe-log-groups --log-group-name-prefix /aws/lambda/aws-daily-usage-report

# View recent logs
aws logs tail /aws/lambda/aws-daily-usage-report-AWSUsageLambdaFunction-XXXXX --follow
```

**Check Execution:**
- Go to **Lambda Console** → Your function → **Monitor** tab
- View invocations, errors, duration
- Click **"View logs in CloudWatch"**

### Updating the Stack

**Update parameters only:**
```bash
aws cloudformation update-stack \
  --stack-name aws-daily-usage-report \
  --use-previous-template \
  --parameters \
    ParameterKey=FromEmail,UsePreviousValue=true \
    ParameterKey=ToEmail,ParameterValue=newemail@example.com \
    ParameterKey=Schedule,UsePreviousValue=true \
    ParameterKey=AwsRegions,UsePreviousValue=true \
  --capabilities CAPABILITY_IAM
```

**Update Lambda code:**
1. Update `inventory.py`
2. Create new zip: `zip -r daily-usage-report.zip inventory.py`
3. Upload to S3 (same path)
4. Update stack (Lambda will automatically use new code)

---

## 💰 Cost Breakdown

### Resources Monitored

| Resource Type | Data Collected | Cost Calculation |
|--------------|----------------|------------------|
| **EC2 Instances** | Instance ID, Type, OS, State, Launch Time | Hourly price × 730 hours/month (OS-aware) |
| **EBS Volumes** | Volume ID, Type, Size, State, IOPS | Price per GB-month × Volume Size |
| **Elastic IPs** | IP Address, Association Status, Creation | $3.65/month per IP (2023 pricing) |
| **Public IPs** | IP Address, Network Interface | $3.65/month per IP (2023 pricing) |
| **RDS Instances** | Instance ID, Class, Engine, State | Hourly price × 730 hours/month (engine-aware) |

### Pricing Features

✅ **Real-time Pricing** - Fetches current AWS prices via Pricing API  
✅ **Regional Pricing** - Accounts for regional price differences  
✅ **OS Detection** - Different pricing for Windows vs Linux  
✅ **Volume Types** - Accurate pricing for gp2, gp3, io1, io2, st1, sc1  
✅ **RDS Engines** - MySQL, PostgreSQL, Aurora, MariaDB, Oracle, SQL Server  
✅ **Price Caching** - Reduces API calls for efficiency  

### Cost Accuracy

**Important Limitations:**
- ❌ Does NOT include Reserved Instance discounts
- ❌ Does NOT include Savings Plans
- ❌ Does NOT include volume discounts
- ❌ Does NOT include Free Tier usage
- ❌ Does NOT include actual usage patterns (CPU, I/O, network)
- ❌ Data transfer costs not included
- ❌ Snapshot costs not included

**Use this for:** Approximate cost tracking and resource inventory  
**Do NOT use for:** Exact billing predictions

---

## 📧 Email Report Sample

The email report includes:

```
Subject: Your AWS Account usage - Approx. monthly cost: $4198.53

=== Running EC2 Instances: 15 (Approx. monthly cost: $2,847.50) ===
┌────┬────────────┬─────────────┬──────────┬─────────┬──────────────┬─────────┬─────────────┬──────────────┐
│ Sr │ Region     │ InstanceId  │ Name     │ State   │InstanceType  │ OS      │ LaunchTime  │ Cost ($/mo)  │
├────┼────────────┼─────────────┼──────────┼─────────┼──────────────┼─────────┼─────────────┼──────────────┤
│ 1  │ us-east-1  │ i-0abc123   │ WebServer│ running │ t3.medium    │ Linux   │ 2025-09-15  │ 30.37        │
│ 2  │ us-west-2  │ i-0def456   │ AppServer│ running │ m5.large     │ Windows │ 2025-08-22  │ 175.20       │
└────┴────────────┴─────────────┴──────────┴─────────┴──────────────┴─────────┴─────────────┴──────────────┘

=== EBS Volumes: 23 (Approx. monthly cost: $920.00) ===
... (similar table format)

=== Public or Elastic IPs: 8 (Approx. monthly cost: $29.20) ===
... (similar table format)

Note: You may want to release the un-associated Elastic IPs to save costs.

=== RDS Instances: 3 (Approx. monthly cost: $401.83) ===
... (similar table format)

=== Total estimated monthly cost: $4,198.53 ===

Note: The cost shown does not consider pricing elements like Reserved instances etc. 
and hence may not reflect actual cost.
```

---

## 🔧 Troubleshooting

### Issue: Email Not Received

**Check 1: Email Verification**
```bash
aws ses list-verified-email-addresses --region us-east-1
```
- Ensure sender and receiver emails are verified
- Check spam/junk folders

**Check 2: SES Sandbox Mode**
- If in Sandbox: Both sender and receiver must be verified
- Request production access: [SES Production Access](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html)

**Check 3: Lambda Logs**
```bash
aws logs tail /aws/lambda/your-function-name --follow
```
- Look for "Error sending email" messages

### Issue: Lambda Timeout

**Symptom**: Function times out before completing

**Solution**: 
- Current timeout: 900 seconds (15 minutes)
- If still timing out, reduce number of regions
- Or increase timeout (max: 15 minutes)

### Issue: No Pricing Data

**Symptom**: Costs show as $0.00

**Check**: AWS Pricing API access
```bash
aws pricing get-products \
  --service-code AmazonEC2 \
  --filters Type=TERM_MATCH,Field=instanceType,Value=t3.micro \
  --region us-east-1
```

**Solution**:
- Ensure IAM role has `pricing:GetProducts` permission
- Pricing API only works in `us-east-1` and `ap-south-1`

### Issue: Permission Denied

**Symptom**: `AccessDeniedException` or `UnauthorizedOperation`

**Solution**: Check IAM permissions
```bash
# Check current permissions
aws iam get-role-policy \
  --role-name aws-daily-usage-report-AWSUsageLambdaFunctionIamRole-XXXXX \
  --policy-name AWSUsageLambdaFunctionPolicy
```

Required permissions:
- `ec2:Describe*`
- `rds:DescribeDBInstances`
- `pricing:GetProducts`
- `ses:SendRawEmail`
- `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`

### Issue: Stack Creation Failed

**Common causes**:
1. **S3 bucket doesn't exist** - Create bucket first
2. **Lambda zip not found** - Upload zip to correct S3 path
3. **Invalid email format** - Check email regex pattern
4. **IAM permissions** - Ensure you can create IAM roles

**Check stack events**:
```bash
aws cloudformation describe-stack-events --stack-name aws-daily-usage-report
```

### Issue: High Lambda Costs

**Symptom**: Unexpected Lambda charges

**Analysis**:
- Lambda: Free tier = 1M requests/month, 400,000 GB-seconds compute
- This function: ~1 execution/day = 30 executions/month
- Duration: ~30-120 seconds depending on resources
- Cost: Usually $0-$1/month

**Optimization**:
- Reduce scanned regions
- Use price caching (already implemented)
- Adjust schedule (less frequent)

---

## 🔒 Security Considerations

### IAM Best Practices

✅ **Principle of Least Privilege**: The IAM role only has read-only permissions for resources  
✅ **No Write Access**: Cannot modify or delete any AWS resources  
✅ **Resource-Specific**: Permissions limited to required services  

### Email Security

⚠️ **Important**: Email addresses are stored in CloudFormation parameters (visible in stack details)  
✅ Use email distribution lists instead of personal emails  
✅ Regularly review SES verified identities  

### Credential Management

❗ **Never** commit AWS credentials to git  
✅ Use IAM roles for Lambda (automatic)  
✅ Use AWS CLI profiles for local execution  
✅ Rotate access keys regularly  

### Network Security

✅ Lambda runs in AWS-managed VPC  
✅ No inbound connections required  
✅ Only outbound to AWS APIs  

### Recommendations

1. **Enable CloudTrail** - Audit all API calls
2. **Use AWS Organizations** - Centralized management
3. **Tag Resources** - For cost allocation and governance
4. **Regular Reviews** - Periodically audit IAM permissions
5. **Cost Alerts** - Set up AWS Budgets for unexpected costs

---

## ⚠️ Limitations

### Current Limitations

1. **Services**: Only monitors EC2, EBS, EIP, and RDS
   - Not covered: S3, Lambda, CloudFront, NAT Gateways, Load Balancers, etc.

2. **Cost Accuracy**:
   - No Reserved Instance discounts
   - No Savings Plans
   - No Spot Instance pricing
   - No data transfer costs
   - No Free Tier accounting

3. **EBS Volumes**: 
   - Does not include snapshot costs
   - IOPS/throughput charges for gp3, io1, io2 not calculated

4. **Historical Data**: 
   - No trend analysis
   - No month-over-month comparison
   - No cost forecasting

5. **Email Format**: 
   - HTML tables only
   - No charts or graphs
   - No CSV export

6. **Multi-Account**: 
   - Single AWS account only
   - No AWS Organizations support

### Known Issues

- CloudTrail lookup for EIP creation time may fail if CloudTrail not enabled
- Very large environments (1000+ resources) may need timeout adjustment
- Pricing API occasionally returns no results (cached prices used as fallback)

---

## 🤝 Contributing

Contributions are welcome! Here are some ideas:

### Enhancement Ideas

- [ ] Add NAT Gateway monitoring
- [ ] Add Load Balancer monitoring (ALB/NLB/CLB)
- [ ] Add S3 bucket cost estimation
- [ ] Add Lambda function monitoring
- [ ] Historical cost comparison
- [ ] Cost optimization recommendations
- [ ] Multi-account support via AWS Organizations
- [ ] Slack/Teams integration
- [ ] CSV export option
- [ ] Cost charts and graphs
- [ ] Tag-based cost allocation

### How to Contribute

1. Fork the repository
2. Create feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License.

---

## 📞 Support

### Resources

- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/)
- [Amazon SES Documentation](https://docs.aws.amazon.com/ses/)
- [AWS Pricing API Documentation](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html)
- [CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)

### Common Questions

**Q: How much does this solution cost to run?**  
A: Minimal costs:
- Lambda: ~$0-1/month (within free tier)
- SES: $0 for first 62,000 emails/month
- CloudWatch Logs: ~$0.50/month
- Total: Usually under $2/month

**Q: Can I run this hourly instead of daily?**  
A: Yes! Modify the `ScheduleExpression` in CloudFormation or EventBridge to use:
```
cron(0 * * * ? *)  # Every hour
cron(0 */6 * * ? *)  # Every 6 hours
```

**Q: Can I customize the email template?**  
A: Yes! Edit the HTML generation functions in `inventory.py`:
- `html_header_2()`, `html_header_3()`
- `html_para()`, `html_table()`
- Add CSS styling in `lambda_handler()`

**Q: Does this work with AWS Organizations?**  
A: Not currently. You need to deploy in each account separately. Multi-account support is a planned enhancement.

**Q: Why are my costs different from AWS Cost Explorer?**  
A: This tool provides estimates based on list prices. Actual costs differ due to:
- Reserved Instances
- Savings Plans
- Free Tier usage
- Data transfer
- Actual usage patterns
- Other services not monitored

---

## 🎯 Roadmap

### v2.0 (Planned)

- [ ] Multi-account support via AWS Organizations
- [ ] Additional services (NAT, ALB/NLB, S3, Lambda)
- [ ] Cost optimization recommendations
- [ ] Historical trend analysis
- [ ] Slack/Microsoft Teams integration
- [ ] Interactive HTML dashboard
- [ ] Cost forecasting
- [ ] Budget alerts

---

## 🙏 Acknowledgments

- Built for AWS cost transparency and governance
- Uses AWS Pricing API for accurate cost data
- Designed for enterprise cloud cost management

---

**Last Updated**: October 2025  
**Version**: 1.0  
**Author**: Calsoft Inc.

---

For questions, issues, or suggestions, please open an issue in the repository.
