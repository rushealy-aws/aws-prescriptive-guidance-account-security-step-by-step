# ACCT.01 - Set Account-Level Contacts to Valid Email Distribution Lists

## 🎯 What This Control Does

This control ensures AWS can reach the right people in your organization by setting up proper contact information using email distribution lists instead of individual email addresses. This prevents communication issues when team members leave or change roles.

## 🔍 Why This Matters

- **Business continuity**: Ensures AWS can always reach someone about your account
- **Security notifications**: Critical security alerts won't go to inactive email addresses
- **Billing alerts**: Important billing notifications reach the right team
- **Operational updates**: Service notifications reach appropriate personnel

## ⏱️ Time Required
**10 minutes**

## 📋 Step-by-Step Instructions

### Step 1: Access Account Settings

1. Sign in to the AWS Management Console as the root user or an administrator
2. Navigate to the [Billing and Cost Management console](https://console.aws.amazon.com/billing/home?#/account)
3. Click on **Account Settings** in the left navigation pane

### Step 2: Update Primary Account Information

1. On the **Account Settings** page, find the **Account Settings** section
2. Click **Edit** next to Account Settings
3. Review and update the following if needed:
   - **Account name**: Use your organization's name
   - **Root user email**: Consider using a distribution list (e.g., `aws-admin@yourcompany.com`)
4. Click **Save changes** when done

### Step 3: Update Contact Information

1. In the **Contact Information** section, click **Edit**
2. Update your contact details:
   - Use your organization's official address
   - Provide a main phone number that reaches your organization
   - Ensure all information is current and accurate
3. Click **Update** to save changes

### Step 4: Set Up Alternate Contacts

This is the most important part! Set up distribution lists for different types of notifications:

1. In the **Alternate Contacts** section, click **Edit**
2. Configure the following contacts:

   **Billing Contact:**
   - Email: `aws-billing@yourcompany.com` (or your finance team's distribution list)
   - Name: "Finance Team" or "Billing Department"
   - Phone: Your finance team's contact number

   **Operations Contact:**
   - Email: `aws-ops@yourcompany.com` (or your operations team's distribution list)
   - Name: "Operations Team" or "DevOps Team"
   - Phone: Your operations team's contact number

   **Security Contact:**
   - Email: `aws-security@yourcompany.com` (or your security team's distribution list)
   - Name: "Security Team" or "IT Security"
   - Phone: Your security team's contact number

3. Click **Update** to save all alternate contacts

## ✅ Verification Steps

After completing the setup:

1. **Confirm all contacts are saved**: Refresh the Account Settings page and verify all information appears correctly
2. **Test distribution lists**: Send a test email to each distribution list to ensure they're working
3. **Document the setup**: Keep a record of which distribution lists you've configured

## 💡 Best Practices

### Email Distribution Lists
- **Create dedicated AWS lists**: Don't reuse general company email lists
- **Include multiple people**: Ensure at least 2-3 people are on each list
- **Regular maintenance**: Review and update distribution lists quarterly
- **Clear naming**: Use descriptive names like `aws-security@company.com`

### Contact Information
- **Keep it current**: Update contact information when people change roles
- **Use 24/7 contacts**: For operations, consider using contacts that are monitored around the clock
- **Geographic considerations**: If you're global, consider time zones for contact preferences

## 🚨 Common Mistakes to Avoid

- ❌ Using personal email addresses that might become inactive
- ❌ Using generic emails that aren't monitored (like `noreply@company.com`)
- ❌ Forgetting to test distribution lists after setup
- ❌ Not updating contacts when team members leave
- ❌ Using the same email for all contact types

## 🔗 Official AWS Documentation

- [Managing your AWS account ID and contact information](https://docs.aws.amazon.com/accounts/latest/reference/manage-acct-update-contact.html)
- [AWS Account Management Reference Guide](https://docs.aws.amazon.com/accounts/latest/reference/)

## ➡️ What's Next?

Great job! You've secured your account's contact information. Next, let's secure the root user access:

**Next Control**: [ACCT.02 - Restrict Use of the Root User](./acct-02.md)

---

[← Back to Main Guide](./README.md) | [Next: ACCT.02 →](./acct-02.md)
