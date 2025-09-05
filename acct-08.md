# ACCT.08 - Prevent Public Access to Private S3 Buckets

## 🎯 What This Control Does

This control prevents accidental public exposure of your S3 buckets by enabling Block Public Access settings. This acts as a safety net to prevent data breaches caused by misconfigured bucket policies or ACLs.

## 🔍 Why This Matters

- **Data breach prevention**: Stops accidental exposure of sensitive data
- **Compliance requirement**: Many regulations require protection of private data
- **Cost protection**: Prevents unexpected charges from public data access
- **Reputation protection**: Avoids embarrassing public data exposures
- **Defense in depth**: Additional layer even if bucket policies are misconfigured

## ⏱️ Time Required
**10 minutes**

## 🛡️ Understanding S3 Public Access

### How Buckets Become Public
S3 buckets can become publicly accessible through:
- **Bucket policies** that grant public permissions
- **Access Control Lists (ACLs)** that allow public access
- **Cross-account access** that's too permissive
- **Misconfigured IAM policies**

### Block Public Access Settings
AWS provides four settings to prevent public access:

1. **BlockPublicAcls**: Blocks new public ACLs
2. **IgnorePublicAcls**: Ignores existing public ACLs
3. **BlockPublicPolicy**: Blocks new public bucket policies
4. **RestrictPublicBuckets**: Restricts access to buckets with public policies

---

## 📋 Step-by-Step Instructions

### Step 1: Enable Account-Level Block Public Access

This is the most important step - it protects all buckets in your account:

1. **Navigate to S3 Console**:
   - Go to [S3 console](https://console.aws.amazon.com/s3)
   - Look for **Block Public Access settings for this account** on the left sidebar

2. **Configure Account Settings**:
   - Click **Edit** next to "Block Public Access settings for this account"
   - **Enable all four settings**:
     ```
     ✅ Block public access to buckets and objects granted through new access control lists (ACLs)
     ✅ Block public access to buckets and objects granted through any access control lists (ACLs)
     ✅ Block public access to buckets and objects granted through new public bucket or access point policies
     ✅ Block public access to buckets and objects granted through any public bucket or access point policies
     ```

3. **Confirm Changes**:
   - Type `confirm` in the confirmation box
   - Click **Confirm**

### Step 2: Review Existing Buckets

1. **List All Buckets**:
   - In S3 console, review the main bucket list
   - Look for any buckets marked as "Public" in the Access column

2. **Check Each Bucket**:
   - Click on each bucket name
   - Go to the **Permissions** tab
   - Review the **Block public access** settings

### Step 3: Configure Individual Bucket Settings

For each bucket that needs individual attention:

1. **Access Bucket Permissions**:
   - Click on the bucket name
   - Go to **Permissions** tab
   - Find **Block public access (bucket settings)**

2. **Enable Block Public Access**:
   - Click **Edit**
   - Enable all four settings (same as account level):
     ```
     ✅ Block public access to buckets and objects granted through new access control lists (ACLs)
     ✅ Block public access to buckets and objects granted through any access control lists (ACLs)
     ✅ Block public access to buckets and objects granted through new public bucket or access point policies
     ✅ Block public access to buckets and objects granted through any public bucket or access point policies
     ```
   - Click **Save changes**
   - Type `confirm` and click **Confirm**

### Step 4: Handle Legitimate Public Buckets

If you have buckets that legitimately need to be public (like static websites):

1. **Document the need**: Clearly document why the bucket must be public
2. **Use specific permissions**: Grant only the minimum necessary public access
3. **Regular review**: Review public buckets monthly
4. **Consider alternatives**: Use CloudFront distributions instead of direct S3 public access

#### Example: Static Website Bucket
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-website-bucket/*"
        }
    ]
}
```

## ✅ Verification Steps

### Check Account-Level Settings
1. **Verify account settings**:
   - Go to S3 console main page
   - Confirm "Block Public Access is on for this account" appears
   - All four settings should show as enabled

### Test Public Access Prevention
1. **Create a test bucket**:
   - Create a new S3 bucket
   - Try to make it public through bucket policy
   - Verify the action is blocked

2. **Test existing buckets**:
   - Try to add public ACLs to existing buckets
   - Verify these actions are blocked
   - Check that legitimate private access still works

### Review with Trusted Advisor
1. **Check Trusted Advisor**:
   - Go to [Trusted Advisor console](https://console.aws.amazon.com/support/home#/trustedadvisor)
   - Look for S3 bucket permissions checks
   - Verify no buckets are flagged as publicly accessible

## 💡 Best Practices

### Account Management
- **Enable at account level**: Always start with account-level settings
- **Default deny**: Make "block public access" the default for new buckets
- **Regular audits**: Review public access settings monthly
- **Automation**: Use AWS Config rules to monitor compliance

### Bucket-Specific Settings
- **Individual review**: Check each bucket's specific needs
- **Least privilege**: Grant minimum necessary permissions
- **Documentation**: Document any exceptions and their justification
- **Monitoring**: Set up alerts for changes to public access settings

### Alternative Solutions
- **CloudFront**: Use CloudFront for public content distribution
- **Presigned URLs**: Use presigned URLs for temporary public access
- **API Gateway**: Use API Gateway for controlled public access
- **Application Load Balancer**: Use ALB for web application public access

## 🚨 Common Mistakes to Avoid

- ❌ Only setting bucket-level controls without account-level protection
- ❌ Disabling controls for convenience without proper justification
- ❌ Not documenting legitimate public bucket requirements
- ❌ Forgetting to test legitimate access after enabling controls
- ❌ Not monitoring for changes to public access settings
- ❌ Using public buckets when CloudFront would be more appropriate
- ❌ Not having a process for reviewing public bucket needs

## 🔧 Advanced Configuration

### Using AWS CLI
```bash
# Enable account-level block public access
aws s3control put-public-access-block \
    --account-id 123456789012 \
    --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

# Enable for specific bucket
aws s3api put-public-access-block \
    --bucket my-bucket-name \
    --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

# Check current settings
aws s3api get-public-access-block --bucket my-bucket-name
```

### AWS Config Rules
Set up AWS Config to monitor compliance:

```json
{
    "ConfigRuleName": "s3-bucket-public-access-prohibited",
    "Source": {
        "Owner": "AWS",
        "SourceIdentifier": "S3_BUCKET_PUBLIC_ACCESS_PROHIBITED"
    }
}
```

### CloudFormation Template
```yaml
Resources:
  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub "${AWS::StackName}-private-bucket"
      PublicAccessBlockConfiguration:
        BlockPublicAcls: true
        BlockPublicPolicy: true
        IgnorePublicAcls: true
        RestrictPublicBuckets: true
```

## 📊 Monitoring and Alerting

### CloudWatch Metrics
Monitor these metrics:
- `NumberOfObjects` - Track object count changes
- `BucketSizeBytes` - Monitor storage usage
- `AllRequests` - Track access patterns

### CloudTrail Events to Monitor
- `PutBucketAcl`
- `PutBucketPolicy` 
- `PutPublicAccessBlock`
- `DeletePublicAccessBlock`

### Sample CloudWatch Alarm
```bash
aws cloudwatch put-metric-alarm \
    --alarm-name "S3-Public-Access-Block-Disabled" \
    --alarm-description "Alert when S3 public access block is disabled" \
    --metric-name "PublicAccessBlockDisabled" \
    --namespace "AWS/S3" \
    --statistic "Sum" \
    --period 300 \
    --threshold 1 \
    --comparison-operator "GreaterThanOrEqualToThreshold"
```

## 🔗 Official AWS Documentation

- [Configuring block public access settings for your S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/configuring-block-public-access-bucket.html)
- [Configuring block public access settings for your account](https://docs.aws.amazon.com/AmazonS3/latest/userguide/configuring-block-public-access-account.html)
- [Amazon S3 Block Public Access](https://aws.amazon.com/s3/features/block-public-access/)

## ➡️ What's Next?

Perfect! You've secured your S3 buckets from accidental public exposure. Now let's clean up unused network resources:

**Next Control**: [ACCT.09 - Delete Unused VPCs, Subnets, and Security Groups](./acct-09.md)

---

[← Previous: ACCT.07](./acct-07.md) | [Back to Main Guide](./README.md) | [Next: ACCT.09 →](./acct-09.md)
