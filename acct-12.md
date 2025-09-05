# ACCT.12 - Monitor and Resolve High-Risk Issues Using Trusted Advisor

## 🎯 What This Control Does

This control establishes regular monitoring of AWS Trusted Advisor to identify and resolve security, performance, cost optimization, and reliability issues in your AWS environment. Trusted Advisor acts as your automated AWS consultant, scanning your infrastructure for best practice violations.

## 🔍 Why This Matters

- **Proactive issue detection**: Identifies problems before they become critical
- **Security recommendations**: Finds security vulnerabilities and misconfigurations
- **Cost optimization**: Identifies opportunities to reduce AWS costs
- **Performance improvements**: Suggests ways to improve application performance
- **Compliance support**: Helps maintain security and operational best practices

## ⏱️ Time Required
**10 minutes setup + ongoing weekly reviews**

## 🔍 Understanding Trusted Advisor

### Check Categories
1. **Security**: Security groups, IAM usage, MFA, public S3 buckets
2. **Cost Optimization**: Unused resources, right-sizing opportunities
3. **Performance**: Service limits, over-utilized resources
4. **Fault Tolerance**: Backup configurations, multi-AZ deployments
5. **Service Limits**: Usage approaching service quotas

### Check Status Colors
- 🔴 **Red (Action Recommended)**: Issues that should be addressed immediately
- 🟡 **Yellow (Investigation Recommended)**: Items that should be reviewed
- 🟢 **Green (No Problems Detected)**: Everything looks good
- 🔵 **Blue (Informational)**: General information about your resources

### Support Plan Requirements
- **Basic/Developer**: Limited checks (7 core checks)
- **Business/Enterprise**: Full Trusted Advisor (100+ checks)
- **Enterprise**: Additional features like API access and weekly email reports

---

## 📋 Step-by-Step Instructions

### Step 1: Access Trusted Advisor

1. **Navigate to Trusted Advisor**:
   - Go to [Trusted Advisor console](https://console.aws.amazon.com/support/home#/trustedadvisor)
   - Or from AWS Support console, click **Trusted Advisor**

2. **Review available checks**:
   - Note which checks are available based on your support plan
   - If you have Basic support, consider upgrading for full access

### Step 2: Review Security Checks (Priority)

Focus on security-related checks first:

#### Critical Security Checks to Review:
1. **Security Groups - Specific Ports Unrestricted**
   - Look for security groups allowing 0.0.0.0/0 access
   - Focus on ports 22 (SSH), 3389 (RDP), 1433 (SQL Server), 3306 (MySQL)

2. **Security Groups - Unrestricted Access**
   - Check for overly permissive security groups
   - Review any groups allowing all traffic (0.0.0.0/0 on all ports)

3. **IAM Use**
   - Verify you're using IAM users instead of root user
   - Check for proper IAM configuration

4. **MFA on Root Account**
   - Confirm MFA is enabled on root user
   - Should show green if you completed ACCT.05

5. **S3 Bucket Permissions**
   - Look for buckets with public read or write permissions
   - Should show improvements if you completed ACCT.08

### Step 3: Address Red (Action Recommended) Items

For each red item:

1. **Click on the check name** to see details
2. **Review the affected resources**
3. **Understand the risk** described in the check
4. **Take corrective action**:

#### Example: Security Group with Unrestricted Access
```
Problem: Security group allows 0.0.0.0/0 access on port 22
Risk: Anyone on the internet can attempt SSH connections
Action: 
1. Go to EC2 → Security Groups
2. Find the flagged security group
3. Edit inbound rules
4. Replace 0.0.0.0/0 with specific IP ranges or remove the rule
```

#### Example: S3 Bucket with Public Access
```
Problem: S3 bucket allows public read access
Risk: Data could be accessed by unauthorized users
Action:
1. Go to S3 console
2. Find the flagged bucket
3. Review bucket policy and ACLs
4. Remove public access or enable Block Public Access
```

### Step 4: Set Up Regular Monitoring

#### Weekly Review Process
1. **Schedule weekly reviews**: Set calendar reminder for same day/time each week
2. **Create a checklist**:
   ```
   □ Check for new red items
   □ Review yellow items for potential action
   □ Verify previous fixes are still in place
   □ Document any new issues found
   ```

#### For Business/Enterprise Support Plans
1. **Enable email notifications**:
   - In Trusted Advisor console, click **Preferences**
   - Set up **Weekly email notification**
   - Add team email addresses
   - Choose notification frequency and language

2. **Configure notification preferences**:
   ```
   ✅ Weekly summary email
   ✅ Include all check categories
   ✅ Send to security team distribution list
   Language: English
   ```

### Step 5: Create Response Procedures

Document how to handle different types of findings:

#### Security Issues (Red Items)
1. **Immediate action required**
2. **Assess impact and risk**
3. **Implement fix within 24 hours**
4. **Verify fix resolves the issue**
5. **Document the resolution**

#### Cost Optimization (Yellow/Red Items)
1. **Review monthly during cost optimization sessions**
2. **Evaluate business impact of changes**
3. **Implement changes during maintenance windows**
4. **Monitor for any negative impacts**

#### Performance Issues
1. **Correlate with application performance metrics**
2. **Plan implementation during low-usage periods**
3. **Test changes in non-production first**
4. **Monitor performance after changes**

## ✅ Verification Steps

### Initial Review
1. **Complete first full review**:
   - Go through all available checks
   - Document current red and yellow items
   - Create action plan for addressing issues

2. **Verify critical security items**:
   - Confirm no unrestricted security groups (except where justified)
   - Verify no public S3 buckets (except where intended)
   - Check that MFA is enabled on root account

### Ongoing Monitoring
1. **Weekly check completion**:
   - Review new items each week
   - Track progress on resolving issues
   - Maintain documentation of findings and resolutions

2. **Trend analysis**:
   - Monitor if issues are increasing or decreasing
   - Identify recurring problems
   - Adjust processes to prevent repeat issues

## 💡 Best Practices

### Regular Monitoring
- **Consistent schedule**: Same day/time each week
- **Team responsibility**: Assign specific team members to review
- **Documentation**: Keep records of findings and resolutions
- **Escalation**: Define when to escalate issues to management

### Issue Prioritization
1. **Security issues**: Always highest priority
2. **Service limit warnings**: High priority (can cause outages)
3. **Cost optimization**: Medium priority (review monthly)
4. **Performance**: Medium priority (unless affecting users)
5. **Fault tolerance**: Medium priority (plan during maintenance)

### Response Management
- **SLA for fixes**: Define timeframes for different issue types
- **Change management**: Use proper change control for fixes
- **Testing**: Test fixes in non-production when possible
- **Communication**: Keep stakeholders informed of issues and resolutions

## 🚨 Common Mistakes to Avoid

- ❌ Ignoring Trusted Advisor recommendations
- ❌ Only checking when there's a problem (should be proactive)
- ❌ Not prioritizing security issues appropriately
- ❌ Making changes without understanding the impact
- ❌ Not documenting resolutions (leads to repeat issues)
- ❌ Not having a regular review schedule
- ❌ Dismissing yellow items without investigation

## 🔧 Advanced Usage

### API Access (Business/Enterprise Support)
```bash
# Get Trusted Advisor checks
aws support describe-trusted-advisor-checks --language en

# Get specific check result
aws support describe-trusted-advisor-check-result \
    --check-id Pfx0RwqBli \
    --language en
```

### Automation with Lambda
```python
import boto3
import json

def lambda_handler(event, context):
    support = boto3.client('support', region_name='us-east-1')
    
    # Get all checks
    checks = support.describe_trusted_advisor_checks(language='en')
    
    red_items = []
    for check in checks['checks']:
        result = support.describe_trusted_advisor_check_result(
            checkId=check['id'],
            language='en'
        )
        
        if result['result']['status'] == 'error':
            red_items.append({
                'name': check['name'],
                'category': check['category'],
                'description': check['description']
            })
    
    # Send alert if red items found
    if red_items:
        # Send to SNS, Slack, etc.
        print(f"Found {len(red_items)} critical issues")
    
    return {'statusCode': 200, 'body': json.dumps(red_items)}
```

### Integration with Other Tools
- **AWS Config**: Use Config rules to continuously monitor some Trusted Advisor checks
- **CloudWatch**: Create custom metrics from Trusted Advisor data
- **ServiceNow**: Create tickets automatically for red items
- **Jira**: Track resolution of Trusted Advisor findings

## 📊 Tracking and Metrics

### Key Metrics to Track
- **Number of red items over time**
- **Time to resolution for security issues**
- **Cost savings from optimization recommendations**
- **Percentage of checks passing (green status)**

### Monthly Reporting
Create monthly reports showing:
- Trusted Advisor score trends
- Issues resolved vs. new issues found
- Cost optimization opportunities identified
- Security improvements implemented

## 🔗 Official AWS Documentation

- [AWS Trusted Advisor](https://aws.amazon.com/support/trusted-advisor/)
- [Trusted Advisor check reference](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor-check-reference.html)
- [Getting started with Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/get-started-with-aws-trusted-advisor.html)
- [Trusted Advisor API reference](https://docs.aws.amazon.com/awssupport/latest/APIReference/API_Operations_AWS_Support.html)

## 🎉 Congratulations!

You've completed all 12 security controls! Your AWS account now has a solid security foundation. Here's what you've accomplished:

✅ **Account Management**: Proper contacts and root user security  
✅ **Access Control**: MFA, strong passwords, and proper permissions  
✅ **Monitoring**: CloudTrail logging, cost alerts, and threat detection  
✅ **Infrastructure Security**: Secured S3 buckets and cleaned up unused resources  
✅ **Ongoing Monitoring**: Trusted Advisor for continuous improvement  

## ➡️ What's Next?

### Immediate Actions
1. **Test your security**: Verify all controls are working as expected
2. **Document your setup**: Create runbooks for your security procedures
3. **Train your team**: Ensure everyone understands the new security measures

### Ongoing Security Journey
- **Regular reviews**: Schedule monthly security reviews
- **Stay updated**: Follow AWS security best practices and updates
- **Expand coverage**: Consider additional security services like AWS Config, Security Hub
- **Compliance**: Align with relevant compliance frameworks (SOC 2, ISO 27001, etc.)

---

[← Previous: ACCT.11](./acct-11.md) | [Back to Main Guide](./README.md)

**🎯 You've successfully implemented the AWS Security Baseline! Your AWS account is now significantly more secure.**
