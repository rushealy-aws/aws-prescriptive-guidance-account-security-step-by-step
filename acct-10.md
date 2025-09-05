# ACCT.10 - Configure AWS Budgets to Monitor Your Spending

## 🎯 What This Control Does

This control sets up AWS Budgets to monitor your spending and alert you when costs exceed expected thresholds. This helps detect unexpected charges that could indicate security incidents, resource misconfigurations, or unauthorized usage.

## 🔍 Why This Matters

- **Security monitoring**: Unusual spending patterns can indicate compromised accounts
- **Cost control**: Prevents surprise bills from runaway resources
- **Operational awareness**: Helps you understand your AWS usage patterns
- **Budget planning**: Enables better financial planning and forecasting
- **Early warning system**: Alerts before costs become problematic

## ⏱️ Time Required
**10 minutes**

## 💰 Understanding AWS Budgets

### Types of Budgets
- **Cost budgets**: Monitor spending against a dollar amount
- **Usage budgets**: Monitor usage of specific services (hours, GB, etc.)
- **Savings Plans budgets**: Monitor Savings Plans utilization
- **Reservation budgets**: Monitor Reserved Instance utilization

### Alert Types
- **Actual**: Alerts when actual spending exceeds threshold
- **Forecasted**: Alerts when forecasted spending will exceed threshold
- **Usage**: Alerts based on service usage metrics

---

## 📋 Step-by-Step Instructions

### Step 1: Access AWS Budgets

1. **Navigate to AWS Budgets**:
   - Go to [AWS Budgets console](https://console.aws.amazon.com/billing/home#/budgets)
   - Or from Billing dashboard, click **Budgets** in left navigation

2. **Review existing budgets**:
   - Check if any budgets already exist
   - Note any default budgets AWS may have created

### Step 2: Create Your First Budget (Monthly Cost Budget)

1. **Start budget creation**:
   - Click **Create budget**
   - Select **Cost budget**
   - Click **Next**

2. **Configure budget details**:
   ```
   Budget name: Monthly-Total-Cost-Budget
   Period: Monthly
   Budget renewal type: Recurring
   Start month: Current month
   Budgeting method: Fixed
   Budget amount: $[Your expected monthly spend]
   ```

3. **Set budget scope**:
   ```
   ✅ All AWS services (recommended for first budget)
   ✅ All linked accounts (if using Organizations)
   ✅ All regions
   ✅ All charge types
   ```

### Step 3: Configure Alerts

Set up multiple alert thresholds for better monitoring:

#### Alert 1: Early Warning (50% of budget)
```
Alert threshold: 50% of budgeted amount
Threshold type: Actual
Email recipients: [Your team's email addresses]
```

#### Alert 2: Attention Required (80% of budget)
```
Alert threshold: 80% of budgeted amount  
Threshold type: Actual
Email recipients: [Your team's email addresses]
```

#### Alert 3: Critical Alert (100% of budget)
```
Alert threshold: 100% of budgeted amount
Threshold type: Actual
Email recipients: [Your team's email addresses]
```

#### Alert 4: Forecasted Overage (100% forecasted)
```
Alert threshold: 100% of budgeted amount
Threshold type: Forecasted
Email recipients: [Your team's email addresses]
```

### Step 4: Create Service-Specific Budgets

Create additional budgets for your most-used services:

#### EC2 Budget Example
1. **Create new cost budget**:
   ```
   Budget name: EC2-Monthly-Budget
   Service: Amazon Elastic Compute Cloud - Compute
   Budget amount: $[Expected EC2 spend]
   ```

2. **Set alerts at 75% and 100%**

#### S3 Budget Example
1. **Create new cost budget**:
   ```
   Budget name: S3-Monthly-Budget
   Service: Amazon Simple Storage Service
   Budget amount: $[Expected S3 spend]
   ```

### Step 5: Create a Security-Focused Budget

Create a low-threshold budget to catch unexpected activity:

1. **Create anomaly detection budget**:
   ```
   Budget name: Security-Anomaly-Budget
   Budget amount: $50 (or 10% above normal monthly spend)
   Alert threshold: 100% actual
   ```

2. **Purpose**: This budget should alert on any significant unexpected spending that could indicate:
   - Compromised credentials
   - Cryptocurrency mining
   - Data exfiltration
   - Misconfigured resources

## ✅ Verification Steps

### Test Budget Alerts
1. **Check email delivery**:
   - Verify alert emails are being delivered
   - Check spam folders for budget notifications
   - Test with a very low budget if needed

2. **Review budget accuracy**:
   - Compare budget forecasts with actual usage
   - Adjust thresholds based on actual spending patterns

### Monitor Budget Performance
1. **Weekly reviews**: Check budget status weekly
2. **Monthly analysis**: Review which budgets triggered alerts and why
3. **Quarterly adjustments**: Update budget amounts based on usage trends

## 💡 Best Practices

### Budget Strategy
- **Start conservative**: Begin with lower budgets and adjust upward
- **Multiple thresholds**: Use 50%, 80%, and 100% alerts for graduated warnings
- **Service-specific budgets**: Create budgets for your top 3-5 AWS services
- **Team budgets**: Consider separate budgets for different teams or projects

### Alert Configuration
- **Distribution lists**: Use team email lists, not individual addresses
- **Escalation**: Higher thresholds should alert more senior team members
- **Frequency**: Don't set alerts too sensitive to avoid alert fatigue
- **Documentation**: Document what actions to take when each alert fires

### Cost Management
- **Regular reviews**: Review and adjust budgets monthly
- **Trend analysis**: Look for spending trends and patterns
- **Cost optimization**: Use budget data to identify optimization opportunities
- **Forecasting**: Use AWS Cost Explorer for detailed forecasting

## 🚨 Common Mistakes to Avoid

- ❌ Setting budgets too high (won't catch problems early)
- ❌ Setting budgets too low (causes alert fatigue)
- ❌ Not including all relevant services in budget scope
- ❌ Using only individual email addresses for alerts
- ❌ Not testing alert delivery
- ❌ Creating budgets but never reviewing them
- ❌ Not adjusting budgets as usage patterns change
- ❌ Ignoring forecasted spending alerts

## 🔧 Advanced Budget Configuration

### Using AWS CLI
```bash
# Create a cost budget
aws budgets create-budget \
    --account-id 123456789012 \
    --budget '{
        "BudgetName": "Monthly-Cost-Budget",
        "BudgetLimit": {
            "Amount": "100.0",
            "Unit": "USD"
        },
        "TimeUnit": "MONTHLY",
        "BudgetType": "COST"
    }' \
    --notifications-with-subscribers '[
        {
            "Notification": {
                "NotificationType": "ACTUAL",
                "ComparisonOperator": "GREATER_THAN",
                "Threshold": 80.0
            },
            "Subscribers": [
                {
                    "SubscriptionType": "EMAIL",
                    "Address": "admin@company.com"
                }
            ]
        }
    ]'
```

### CloudFormation Template
```yaml
Resources:
  MonthlyCostBudget:
    Type: AWS::Budgets::Budget
    Properties:
      Budget:
        BudgetName: Monthly-Cost-Budget
        BudgetLimit:
          Amount: 100
          Unit: USD
        TimeUnit: MONTHLY
        BudgetType: COST
      NotificationsWithSubscribers:
        - Notification:
            NotificationType: ACTUAL
            ComparisonOperator: GREATER_THAN
            Threshold: 80
          Subscribers:
            - SubscriptionType: EMAIL
              Address: admin@company.com
```

### Usage Budget Example
```bash
# Create EC2 usage budget (instance hours)
aws budgets create-budget \
    --account-id 123456789012 \
    --budget '{
        "BudgetName": "EC2-Usage-Budget",
        "BudgetLimit": {
            "Amount": "1000.0",
            "Unit": "Hrs"
        },
        "TimeUnit": "MONTHLY",
        "BudgetType": "USAGE",
        "CostFilters": {
            "Service": ["Amazon Elastic Compute Cloud - Compute"]
        }
    }'
```

## 📊 Budget Monitoring and Analysis

### Key Metrics to Track
- **Actual vs. budgeted spending**
- **Month-over-month growth**
- **Service-level spending trends**
- **Forecasted vs. actual spending accuracy**

### Regular Review Process
1. **Weekly**: Check for any budget alerts
2. **Monthly**: Review all budget performance
3. **Quarterly**: Adjust budget amounts and thresholds
4. **Annually**: Review overall budgeting strategy

### Integration with Other Tools
- **Cost Explorer**: Use for detailed cost analysis
- **Cost and Usage Reports**: For granular spending data
- **CloudWatch**: Create custom metrics from budget data
- **SNS**: Send budget alerts to Slack or other systems

## 📱 Mobile Monitoring

### AWS Mobile App
- Download AWS Console Mobile App
- Enable budget notifications
- Check spending on-the-go

### Third-Party Tools
- **CloudHealth**: Advanced cost management
- **CloudCheckr**: Cost optimization and governance
- **Spot.io**: Cost optimization for compute resources

## 🔗 Official AWS Documentation

- [Creating a cost budget](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-create.html#create-cost-budget)
- [AWS Budgets User Guide](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html)
- [Best practices for AWS Budgets](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-best-practices.html)

## ➡️ What's Next?

Excellent! You now have cost monitoring in place to detect unusual spending patterns. Let's set up threat detection with Amazon GuardDuty:

**Next Control**: [ACCT.11 - Enable and Respond to GuardDuty Notifications](./acct-11.md)

---

[← Previous: ACCT.09](./acct-09.md) | [Back to Main Guide](./README.md) | [Next: ACCT.11 →](./acct-11.md)
