# ACCT.02 - Restrict Use of the Root User

## 🎯 What This Control Does

This control limits the use of your AWS root user account to only essential tasks that absolutely require root privileges. The root user has unrestricted access to everything in your AWS account, so minimizing its use is critical for security.

## 🔍 Why This Matters

- **Unlimited power**: The root user can do anything in your account - including closing it
- **Cannot be restricted**: Root user permissions cannot be limited by IAM policies
- **High-value target**: Attackers specifically target root credentials
- **Audit trail**: Fewer root user actions make security monitoring more effective

## ⏱️ Time Required
**5 minutes**

## 📋 What You Need to Know First

### Tasks That REQUIRE Root User Access
The root user should ONLY be used for these specific tasks:
- Changing account settings (name, email, root password)
- Restoring IAM user permissions if accidentally removed
- Activating IAM access to billing information
- Closing your AWS account
- Changing AWS support plans
- Configuring S3 bucket to use MFA Delete
- Submitting reverse DNS requests for EC2 instances

### Everything Else Should Use IAM
For daily operations, use IAM users, roles, or federated access instead of root.

## 📋 Step-by-Step Instructions

### Step 1: Secure the Root User (If Not Done Already)

1. **Enable MFA for root user** (we'll cover this in detail in ACCT.05)
   - This is your most important security step
   - Use a hardware MFA device if possible for maximum security

2. **Use a strong, unique password**
   - Generate a complex password using a password manager
   - Never reuse this password anywhere else

### Step 2: Create an Administrative IAM User

Instead of using root for daily tasks, create an administrative IAM user:

1. Sign in to the [IAM Console](https://console.aws.amazon.com/iam) as root user
2. In the navigation pane, click **Users**
3. Click **Create user**
4. Configure the user:
   - **User name**: `admin` or `your-name-admin`
   - **Provide user access to AWS Management Console**: ✅ Check this
   - **Console password**: Choose "Custom password" and create a strong password
   - **Users must create a new password at next sign-in**: ✅ Check this for security
5. Click **Next**

### Step 3: Assign Administrative Permissions

1. On the permissions page, select **Attach policies directly**
2. Search for and select **AdministratorAccess**
   - This gives full access to AWS services (but not account-level settings)
3. Click **Next**
4. Review the configuration and click **Create user**

### Step 4: Set Up MFA for the Administrative User

1. Click on the newly created user
2. Go to the **Security credentials** tab
3. In the **Multi-factor authentication** section, click **Assign MFA device**
4. Follow the setup wizard (detailed steps in ACCT.05)

### Step 5: Test the Administrative User

1. **Sign out** of the root user session
2. **Sign in** using the new administrative user credentials
3. **Verify access**: Try accessing different AWS services to confirm permissions work
4. **Complete MFA setup**: If prompted, set up MFA during first login

### Step 6: Secure Root User Credentials

1. **Store root credentials securely**:
   - Use a password manager shared with your team
   - Store MFA device in a secure, accessible location
   - Document who has access to root credentials

2. **Create a root user access procedure**:
   - Document when root access is needed
   - Require approval from multiple team members
   - Log all root user activities

## ✅ Verification Steps

1. **Confirm administrative user works**: Successfully log in and access AWS services
2. **Test permissions**: Verify the admin user can perform necessary tasks
3. **Root user secured**: Confirm root user has MFA enabled and strong password
4. **Documentation complete**: Team knows how to access root when needed

## 💡 Best Practices

### Root User Security
- **Physical MFA device**: Use hardware tokens when possible
- **Secure storage**: Keep root credentials in a team-accessible but secure location
- **Regular rotation**: Change root password periodically (quarterly recommended)
- **Monitor usage**: Set up alerts for any root user activity

### Administrative Users
- **Individual accounts**: Create separate admin users for each team member
- **Principle of least privilege**: Even for admins, only grant necessary permissions
- **Regular review**: Audit administrative access quarterly
- **MFA required**: Always require MFA for administrative access

### Access Procedures
- **Document everything**: Clear procedures for when root access is needed
- **Approval process**: Require multiple people to approve root access
- **Activity logging**: Log and review all root user activities
- **Emergency access**: Have a secure emergency access procedure

## 🚨 Common Mistakes to Avoid

- ❌ Using root user for daily administrative tasks
- ❌ Sharing root credentials via insecure methods (email, chat)
- ❌ Not enabling MFA on root user
- ❌ Creating only one administrative user for the whole team
- ❌ Not documenting root access procedures
- ❌ Forgetting to test administrative user access before restricting root

## 🔗 Official AWS Documentation

- [Tasks that require root user credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_root-user.html#root-user-tasks)
- [AWS security credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/security-creds.html)
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

## ➡️ What's Next?

Excellent! You've secured your root user and created administrative access. Now let's set up proper user access for your team:

**Next Control**: [ACCT.03 - Configure Console Access for Each User](./acct-03.md)

---

[← Previous: ACCT.01](./acct-01.md) | [Back to Main Guide](./README.md) | [Next: ACCT.03 →](./acct-03.md)
