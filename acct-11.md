# ACCT.11 - Enable and Respond to GuardDuty Notifications

## 🎯 What This Control Does

This control enables Amazon GuardDuty, AWS's intelligent threat detection service, and sets up automated notifications for security findings. GuardDuty continuously monitors your AWS environment for malicious activity and provides detailed security alerts.

## 🔍 Why This Matters

- **Threat detection**: Identifies malicious activity like cryptocurrency mining, data exfiltration, and compromised instances
- **24/7 monitoring**: Continuous monitoring without requiring dedicated security staff
- **Machine learning**: Uses ML to detect anomalous behavior patterns
- **Threat intelligence**: Leverages AWS threat intelligence and third-party feeds
- **Incident response**: Provides detailed findings to help investigate and respond to threats

## ⏱️ Time Required
**20 minutes**

## 🛡️ Understanding GuardDuty

### What GuardDuty Monitors
- **DNS logs**: Detects communication with malicious domains
- **VPC Flow Logs**: Identifies suspicious network traffic patterns
- **CloudTrail logs**: Monitors for unusual API activity
- **S3 data events**: Detects suspicious S3 access patterns (if enabled)

### Types of Threats Detected
- **Cryptocurrency mining**: Unauthorized mining activity
- **Compromised instances**: Instances communicating with known bad actors
- **Data exfiltration**: Unusual data transfer patterns
- **Reconnaissance**: Port scanning and other reconnaissance activities
- **Credential compromise**: Unusual API calls or access patterns

---

## 📋 Step-by-Step Instructions

### Step 1: Enable GuardDuty

1. **Navigate to GuardDuty Console**:
   - Go to [GuardDuty console](https://console.aws.amazon.com/guardduty)
   - Select your primary region (usually where most resources are)

2. **Enable GuardDuty**:
   - Click **Get Started**
   - Review the service permissions
   - Click **Enable GuardDuty**

3. **Configure initial settings**:
   ```
   ✅ Enable GuardDuty
   ✅ Enable S3 protection (recommended)
   ✅ Enable Malware Protection (if available in your region)
   ✅ Enable Kubernetes protection (if you use EKS)
   ```

### Step 2: Enable GuardDuty in All Used Regions

GuardDuty is region-specific, so enable it in all regions where you have resources:

1. **Switch to each region**:
   - Use the region selector in the top-right
   - Repeat the enablement process for each region

2. **Recommended regions to enable** (at minimum):
   - Your primary working region
   - us-east-1 (for global services)
   - Any region where you have resources

### Step 3: Set Up SNS Topic for Notifications

1. **Create SNS Topic**:
   - Go to [SNS console](https://console.aws.amazon.com/sns)
   - Click **Create topic**
   - Configure:
     ```
     Type: Standard
     Name: GuardDuty-Security-Alerts
     Display name: GuardDuty Security Alerts
     ```

2. **Create subscriptions**:
   - Click **Create subscription**
   - Protocol: **Email**
   - Endpoint: Your security team's email address
   - Click **Create subscription**
   - Check email and confirm subscription

### Step 4: Create CloudWatch Event Rule

1. **Navigate to EventBridge**:
   - Go to [EventBridge console](https://console.aws.amazon.com/events)
   - Click **Create rule**

2. **Configure event rule**:
   ```
   Name: GuardDuty-Finding-Alert
   Description: Alert on GuardDuty findings
   Event bus: default
   Rule type: Rule with an event pattern
   ```

3. **Set event pattern**:
   ```json
   {
     "source": ["aws.guardduty"],
     "detail-type": ["GuardDuty Finding"],
     "detail": {
       "severity": [4.0, 4.1, 4.2, 4.3, 4.4, 4.5, 4.6, 4.7, 4.8, 4.9, 5.0, 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8, 5.9, 6.0, 6.1, 6.2, 6.3, 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 7.8, 7.9, 8.0, 8.1, 8.2, 8.3, 8.4, 8.5, 8.6, 8.7, 8.8, 8.9, 9.0, 9.1, 9.2, 9.3, 9.4, 9.5, 9.6, 9.7, 9.8, 9.9, 10.0]
     }
   }
   ```
   (This filters for medium to high severity findings: 4.0-10.0)

4. **Set target**:
   - Target type: **AWS service**
   - Service: **SNS topic**
   - Topic: Select your GuardDuty-Security-Alerts topic

### Step 5: Configure Finding Format (Optional)

To get more readable alerts, you can transform the event:

1. **In the EventBridge rule**:
   - Expand **Additional settings**
   - Configure **Input transformer**:

2. **Input path**:
   ```json
   {
     "severity": "$.detail.severity",
     "type": "$.detail.type", 
     "title": "$.detail.title",
     "description": "$.detail.description",
     "region": "$.detail.region",
     "accountId": "$.detail.accountId"
   }
   ```

3. **Input template**:
   ```
   "🚨 GuardDuty Security Alert 🚨
   
   Severity: <severity>
   Type: <type>
   Title: <title>
   
   Description: <description>
   
   Account: <accountId>
   Region: <region>
   
   Please investigate immediately in the GuardDuty console."
   ```

## ✅ Verification Steps

### Test GuardDuty Setup
1. **Check GuardDuty status**:
   - In GuardDuty console, verify status shows "Enabled"
   - Check that findings are being generated (may take 15-30 minutes)

2. **Generate test finding**:
   - In GuardDuty console, go to **Settings**
   - Click **Generate sample findings**
   - Check that sample findings appear in the console

3. **Test notifications**:
   - Sample findings should trigger your EventBridge rule
   - Check that you receive email notifications
   - Verify the alert format is readable

### Review Initial Findings
1. **Check for existing threats**:
   - Review any real findings that appear
   - Investigate high-severity findings immediately
   - Document any false positives for tuning

## 💡 Best Practices

### GuardDuty Configuration
- **Enable in all regions**: Even regions you don't actively use
- **S3 protection**: Enable for comprehensive coverage
- **Regular review**: Check findings weekly at minimum
- **Severity focus**: Prioritize high and medium severity findings

### Alert Management
- **Severity filtering**: Only alert on medium+ severity (4.0+) to reduce noise
- **Multiple channels**: Consider Slack, PagerDuty, or other alerting systems
- **Escalation**: Set up escalation for high-severity findings
- **Response procedures**: Document how to respond to different finding types

### Cost Management
- **Monitor costs**: GuardDuty charges based on data volume analyzed
- **Optimize data sources**: Disable unnecessary data sources if costs are high
- **Regional strategy**: Consider costs when enabling in many regions
- **Regular review**: Monitor GuardDuty costs in your billing dashboard

## 🚨 Common Mistakes to Avoid

- ❌ Only enabling GuardDuty in one region
- ❌ Not setting up notifications (findings sit unnoticed)
- ❌ Alerting on all findings (creates alert fatigue)
- ❌ Not investigating findings promptly
- ❌ Disabling GuardDuty due to false positives without proper tuning
- ❌ Not documenting response procedures
- ❌ Forgetting to enable S3 protection

## 🔧 Advanced Configuration

### Suppression Rules
Create suppression rules for known false positives:

1. **In GuardDuty console**:
   - Go to **Settings** → **Suppression rules**
   - Click **Add rule**

2. **Example suppression rule**:
   ```
   Rule name: Suppress-Known-Scanner
   Description: Suppress findings from known security scanner
   Criteria:
   - Finding type: Recon:EC2/PortProbeUnprotectedPort
   - Source IP: 192.168.1.100 (your security scanner)
   ```

### Multi-Account Setup
For AWS Organizations:

1. **Enable GuardDuty master account**:
   - Designate security account as GuardDuty master
   - Invite member accounts
   - Centralize findings management

2. **Automated member invitation**:
   ```bash
   aws guardduty create-members \
       --detector-id abcd1234 \
       --account-details AccountId=123456789012,Email=security@company.com
   ```

### Integration with Security Tools

#### Send to Slack
```python
import json
import urllib3

def lambda_handler(event, context):
    # Parse GuardDuty finding
    finding = event['detail']
    
    # Format Slack message
    message = {
        "text": f"🚨 GuardDuty Alert: {finding['title']}",
        "attachments": [{
            "color": "danger" if finding['severity'] >= 7.0 else "warning",
            "fields": [
                {"title": "Severity", "value": finding['severity'], "short": True},
                {"title": "Type", "value": finding['type'], "short": True},
                {"title": "Description", "value": finding['description']}
            ]
        }]
    }
    
    # Send to Slack webhook
    http = urllib3.PoolManager()
    response = http.request('POST', 'YOUR_SLACK_WEBHOOK_URL',
                          body=json.dumps(message),
                          headers={'Content-Type': 'application/json'})
    
    return {'statusCode': 200}
```

## 📊 Understanding GuardDuty Findings

### Severity Levels
- **Low (1.0-3.9)**: Suspicious activity that may be benign
- **Medium (4.0-6.9)**: Suspicious activity that should be investigated
- **High (7.0-8.9)**: Activity that indicates a likely security issue
- **Critical (9.0-10.0)**: Activity that indicates a confirmed security issue

### Common Finding Types
- **Backdoor:EC2/**: Backdoor communication detected
- **CryptoCurrency:EC2/**: Cryptocurrency mining activity
- **Malware:EC2/**: Malware detected on instance
- **Recon:EC2/**: Reconnaissance activity (port scans, etc.)
- **Trojan:EC2/**: Trojan activity detected
- **UnauthorizedAPICall**: Suspicious API activity

### Response Actions by Finding Type
1. **Cryptocurrency mining**: Immediately isolate instance, investigate compromise
2. **Backdoor communication**: Isolate instance, analyze network traffic
3. **Malware detection**: Quarantine instance, run malware analysis
4. **Reconnaissance**: Monitor and potentially block source IPs
5. **API abuse**: Review IAM permissions, rotate credentials

## 🔗 Official AWS Documentation

- [Amazon GuardDuty User Guide](https://docs.aws.amazon.com/guardduty/latest/ug/)
- [Setting up GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html)
- [Creating CloudWatch Events rules for GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings_cloudwatch.html)
- [GuardDuty finding types](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_finding-types-active.html)

## ➡️ What's Next?

Fantastic! You now have intelligent threat detection monitoring your AWS environment. Let's complete your security baseline with Trusted Advisor monitoring:

**Next Control**: [ACCT.12 - Monitor and Resolve High-Risk Issues Using Trusted Advisor](./acct-12.md)

---

[← Previous: ACCT.10](./acct-10.md) | [Back to Main Guide](./README.md) | [Next: ACCT.12 →](./acct-12.md)
