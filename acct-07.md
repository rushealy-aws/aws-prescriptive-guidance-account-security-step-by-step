# ACCT.07 - Deliver CloudTrail Logs to a Protected S3 Bucket

## 🎯 What This Control Does

This control sets up comprehensive logging of all API calls and activities in your AWS account using AWS CloudTrail. These logs are stored in a secure S3 bucket, giving you a complete audit trail of who did what and when in your AWS environment.

## 🔍 Why This Matters

- **Security monitoring**: Detect unauthorized or suspicious activities
- **Compliance requirements**: Many regulations require comprehensive audit logs
- **Incident response**: Investigate security incidents with detailed activity records
- **Operational insights**: Understand how your AWS resources are being used
- **Cost analysis**: Track resource usage patterns for optimization
- **Forensic analysis**: Detailed evidence for security investigations

## ⏱️ Time Required
**25 minutes**

## 📊 Understanding CloudTrail

### What CloudTrail Logs
- **Management events**: Control plane operations (creating/deleting resources)
- **Data events**: Data plane operations (S3 object access, Lambda function executions)
- **Insight events**: Unusual activity patterns (optional, additional cost)

### Default vs. Custom Trails
- **Default**: 90 days of management events in CloudTrail console (free)
- **Custom trail**: Unlimited retention in S3, includes data events, multi-region

---

## 📋 Step-by-Step Instructions

### Step 1: Plan Your CloudTrail Setup

#### Decide on Trail Configuration
- **Single trail for all regions** (recommended for most organizations)
- **Log management events**: Always include these
- **Data events**: Consider for sensitive S3 buckets and Lambda functions
- **KMS events**: Include for production, exclude for development (high volume)

#### Choose S3 Bucket Strategy
- **Dedicated CloudTrail bucket**: Create new bucket just for logs
- **Existing bucket**: Use existing bucket with proper permissions
- **Cross-account**: Send logs to security account (advanced)

### Step 2: Create CloudTrail Trail

1. **Navigate to CloudTrail**:
   - Go to [CloudTrail console](https://console.aws.amazon.com/cloudtrail)
   - Click **Create trail**

2. **Configure Trail Basics**:
   ```
   Trail name: MyOrganization-CloudTrail
   ✅ Enable for all current and new regions
   ✅ Create new S3 bucket
   S3 bucket name: myorg-cloudtrail-logs-[random-suffix]
   Log file prefix: cloudtrail-logs/
   ✅ Encrypt log files with SSE-S3
   ✅ Enable log file validation
   ```

3. **Configure Events to Log**:

   **Management Events**:
   ```
   ✅ Read events (API calls that read resources)
   ✅ Write events (API calls that modify resources)
   ✅ Exclude AWS KMS events (for development/test)
   ❌ Exclude AWS KMS events (for production - include them)
   ```

   **Data Events** (Optional but Recommended):
   ```
   ✅ S3 bucket and object-level operations
   - Select specific sensitive buckets OR
   - Select "All current and future S3 buckets" for comprehensive logging
   
   ✅ Lambda function executions (if using Lambda)
   - Select specific critical functions OR
   - Select "All current and future Lambda functions"
   ```

4. **Review and Create**:
   - Review all settings
   - Click **Create trail**

### Step 3: Secure the S3 Bucket

#### Review Bucket Policy
1. **Go to S3 Console**:
   - Navigate to [S3 console](https://console.aws.amazon.com/s3)
   - Find your CloudTrail bucket

2. **Check Bucket Policy**:
   - Go to **Permissions** tab
   - Review the **Bucket policy**
   - Ensure it only allows CloudTrail service access

#### Add Source ARN Condition (Important Security Step)
1. **Edit the bucket policy** to add source ARN condition:
   ```json
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
               "Resource": "arn:aws:s3:::your-cloudtrail-bucket",
               "Condition": {
                   "StringEquals": {
                       "aws:SourceArn": "arn:aws:cloudtrail:region:account-id:trail/trail-name"
                   }
               }
           },
           {
               "Sid": "AWSCloudTrailWrite",
               "Effect": "Allow",
               "Principal": {
                   "Service": "cloudtrail.amazonaws.com"
               },
               "Action": "s3:PutObject",
               "Resource": "arn:aws:s3:::your-cloudtrail-bucket/*",
               "Condition": {
                   "StringEquals": {
                       "s3:x-amz-acl": "bucket-owner-full-control",
                       "aws:SourceArn": "arn:aws:cloudtrail:region:account-id:trail/trail-name"
                   }
               }
           }
       ]
   }
   ```

#### Enable MFA Delete (Highly Recommended)
1. **Use AWS CLI** (cannot be done via console):
   ```bash
   # First, configure MFA for your CLI session
   aws sts get-session-token --serial-number arn:aws:iam::ACCOUNT:mfa/USERNAME --token-code MFA-CODE
   
   # Then enable MFA Delete
   aws s3api put-bucket-versioning \
       --bucket your-cloudtrail-bucket \
       --versioning-configuration Status=Enabled,MFADelete=Enabled \
       --mfa "arn:aws:iam::ACCOUNT:mfa/USERNAME MFA-CODE"
   ```

### Step 4: Configure Additional Security

#### Block Public Access
1. **In S3 bucket settings**:
   - Go to **Permissions** tab
   - **Block public access settings**
   - Ensure all options are **enabled**

#### Set Up Lifecycle Policy
1. **Create lifecycle rule**:
   - Go to **Management** tab
   - Click **Create lifecycle rule**
   - Configure transitions:
     ```
     Current versions:
     - Transition to IA after 30 days
     - Transition to Glacier after 90 days
     - Transition to Deep Archive after 365 days
     
     Previous versions:
     - Delete after 90 days (or as per retention policy)
     ```

## ✅ Verification Steps

### Test CloudTrail Logging
1. **Perform test actions**:
   - Create a test S3 bucket
   - Create a test IAM user
   - Delete the test resources

2. **Check logs appear**:
   - Wait 15-20 minutes for log delivery
   - Check S3 bucket for new log files
   - Download and examine a log file

3. **Verify log content**:
   - Confirm your test actions appear in logs
   - Check that user identity is recorded
   - Verify timestamps are accurate

### Test Log File Integrity
1. **Check log file validation**:
   - In CloudTrail console, go to your trail
   - Click **Log file validation**
   - Verify validation is enabled and working

## 💡 Best Practices

### Trail Configuration
- **Multi-region trails**: Always enable for comprehensive coverage
- **Log file validation**: Always enable to detect tampering
- **Separate buckets**: Use dedicated buckets for CloudTrail logs
- **Encryption**: Enable server-side encryption for log files

### S3 Bucket Security
- **MFA Delete**: Enable for production environments
- **Versioning**: Enable to protect against accidental deletion
- **Access logging**: Enable S3 access logging for the CloudTrail bucket
- **Cross-region replication**: Consider for critical environments

### Cost Management
- **Lifecycle policies**: Automatically transition old logs to cheaper storage
- **Data events**: Be selective about which resources to log (can be expensive)
- **KMS events**: Exclude high-volume KMS read events in non-production
- **Regular review**: Monitor CloudTrail costs and adjust as needed

### Monitoring and Alerting
- **CloudWatch integration**: Set up CloudWatch Logs for real-time monitoring
- **Metric filters**: Create filters for suspicious activities
- **SNS notifications**: Alert on specific events or patterns
- **Regular review**: Periodically review logs for unusual activities

## 🚨 Common Mistakes to Avoid

- ❌ Not enabling log file validation
- ❌ Using the same S3 bucket for multiple purposes
- ❌ Not adding source ARN conditions to bucket policy
- ❌ Forgetting to enable MFA Delete for production
- ❌ Not setting up lifecycle policies (leads to high costs)
- ❌ Logging all data events without considering cost impact
- ❌ Not monitoring the CloudTrail service itself
- ❌ Not testing log delivery and integrity

## 🔧 Advanced Configuration

### CloudWatch Logs Integration
```bash
# Create CloudWatch Logs group
aws logs create-log-group --log-group-name CloudTrail/MyOrganization

# Update trail to send logs to CloudWatch
aws cloudtrail put-events-selectors \
    --trail-name MyOrganization-CloudTrail \
    --cloud-watch-logs-log-group-arn arn:aws:logs:region:account:log-group:CloudTrail/MyOrganization \
    --cloud-watch-logs-role-arn arn:aws:iam::account:role/CloudTrail_CloudWatchLogs_Role
```

### Cross-Account Logging
For organizations with multiple AWS accounts:
1. Create centralized logging account
2. Configure cross-account bucket permissions
3. Point all account trails to central bucket

## 📊 Sample Log Analysis Queries

### Using AWS CLI to Search Logs
```bash
# Find all actions by a specific user
aws logs filter-log-events \
    --log-group-name CloudTrail/MyOrganization \
    --filter-pattern "{ $.userIdentity.userName = \"suspicious-user\" }"

# Find all root user activities
aws logs filter-log-events \
    --log-group-name CloudTrail/MyOrganization \
    --filter-pattern "{ $.userIdentity.type = \"Root\" }"

# Find failed login attempts
aws logs filter-log-events \
    --log-group-name CloudTrail/MyOrganization \
    --filter-pattern "{ $.errorCode EXISTS }"
```

## 🔗 Official AWS Documentation

- [Creating a trail using the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html)
- [Amazon S3 bucket policy for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-s3-bucket-policy-for-cloudtrail.html)
- [Enabling MFA Delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html)
- [CloudTrail log file examples](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-log-file-examples.html)

## ➡️ What's Next?

Excellent! You now have comprehensive activity logging in place. Let's secure your S3 buckets from public access:

**Next Control**: [ACCT.08 - Prevent Public Access to Private S3 Buckets](./acct-08.md)

---

[← Previous: ACCT.06](./acct-06.md) | [Back to Main Guide](./README.md) | [Next: ACCT.08 →](./acct-08.md)
