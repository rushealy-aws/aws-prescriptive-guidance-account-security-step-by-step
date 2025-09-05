# ACCT.03 - Configure Console Access for Each User

## 🎯 What This Control Does

This control establishes secure access methods for team members to use AWS services. It prioritizes temporary credentials through identity federation, but also provides guidance for creating IAM users when federation isn't immediately feasible.

## 🔍 Why This Matters

- **Security**: Temporary credentials are more secure than long-term access keys
- **Centralized management**: Single identity source for all your applications
- **Reduced risk**: Automatic credential rotation and expiration
- **Compliance**: Better audit trails and access control
- **User experience**: Single sign-on across multiple systems

## ⏱️ Time Required
**20 minutes** (varies based on chosen approach)

## 🛤️ Choose Your Path

### Option A: Identity Federation (Recommended)
**Best for**: Organizations with existing identity providers or planning for growth
**Complexity**: Medium
**Security**: Highest

### Option B: IAM Users (Temporary Solution)
**Best for**: Small teams needing quick setup, planning to migrate to federation later
**Complexity**: Low
**Security**: Good (with proper management)

---

## 🌟 Option A: Identity Federation (Recommended)

### What is Identity Federation?
Identity federation allows your team to use existing corporate credentials (like Google Workspace, Microsoft 365, or Active Directory) to access AWS, eliminating the need for separate AWS passwords.

### Step 1: Choose Your Identity Provider

**AWS IAM Identity Center (Recommended for most)**
- Built-in AWS solution
- Easy to set up and manage
- Integrates with external providers

**External Providers**
- Google Workspace
- Microsoft Azure AD
- Okta
- Ping Identity
- Active Directory Federation Services (ADFS)

### Step 2: Set Up IAM Identity Center

1. **Navigate to IAM Identity Center**:
   - Go to the [IAM Identity Center console](https://console.aws.amazon.com/singlesignon)
   - Click **Enable** if not already enabled

2. **Choose your identity source**:
   - **Identity Center directory**: For managing users directly in AWS
   - **Active Directory**: If you have on-premises AD
   - **External identity provider**: For Google, Microsoft, etc.

3. **Follow the setup wizard**:
   - Configure your chosen identity source
   - Set up user provisioning
   - Configure MFA requirements

### Step 3: Create Permission Sets

1. In IAM Identity Center, go to **Permission sets**
2. Click **Create permission set**
3. Choose a template or create custom:
   - **AdministratorAccess**: For admin users
   - **PowerUserAccess**: For developers
   - **ReadOnlyAccess**: For auditors
   - **Custom**: For specific needs

### Step 4: Assign Users and Groups

1. Go to **AWS accounts** in IAM Identity Center
2. Select your account
3. Click **Assign users or groups**
4. Select users/groups and assign appropriate permission sets

---

## 🔧 Option B: IAM Users (Temporary Solution)

⚠️ **Important**: This approach uses long-term credentials, which present security risks. Plan to migrate to federation when operationally ready.

### Step 1: Plan Your User Structure

Before creating users, plan:
- How many users you need
- What permissions each user requires
- How you'll organize users (groups vs individual permissions)

### Step 2: Create IAM Users

1. **Go to IAM Console**:
   - Navigate to [IAM Users](https://console.aws.amazon.com/iam/home#/users)
   - Click **Create user**

2. **Configure user details**:
   - **User name**: Use a clear naming convention (e.g., `firstname.lastname`)
   - **Provide user access to AWS Management Console**: ✅ Check this
   - **Console password**: Choose "Custom password" and create a strong password
   - **Users must create a new password at next sign-in**: ✅ Recommended

3. **Set permissions** (covered in detail in ACCT.04):
   - Add user to appropriate groups
   - Or attach policies directly (not recommended for multiple users)

4. **Review and create**:
   - Double-check all settings
   - Click **Create user**

### Step 3: Provide Credentials Securely

1. **Download credentials**:
   - Save the sign-in URL, username, and temporary password
   - Use secure methods to share (password manager, encrypted email)

2. **Share sign-in information**:
   - AWS Console URL: `https://your-account-id.signin.aws.amazon.com/console`
   - Username and temporary password
   - Instructions for first login

### Step 4: User First Login Process

Each user should:
1. Sign in with temporary credentials
2. Change password when prompted
3. Set up MFA (covered in ACCT.05)
4. Test access to required services

## ✅ Verification Steps

### For Identity Federation:
1. **Test SSO access**: Users can sign in through identity provider
2. **Verify permissions**: Users have appropriate access levels
3. **Check MFA**: Multi-factor authentication is working
4. **Audit logs**: Access attempts are being logged

### For IAM Users:
1. **Successful login**: Each user can sign in successfully
2. **Password changed**: Users have updated their temporary passwords
3. **MFA configured**: Each user has set up MFA
4. **Appropriate access**: Users can access needed services but not others

## 💡 Best Practices

### Identity Federation
- **Enable MFA**: Always require multi-factor authentication
- **Regular reviews**: Audit access permissions quarterly
- **Principle of least privilege**: Grant minimum necessary permissions
- **Monitor usage**: Set up logging and monitoring for access patterns

### IAM Users (If Used)
- **Individual accounts**: Never share IAM user credentials
- **Strong passwords**: Enforce strong password policies
- **Regular rotation**: Rotate passwords regularly
- **MFA mandatory**: Require MFA for all users
- **Plan migration**: Prepare to move to federation

### General
- **Document procedures**: Clear onboarding/offboarding processes
- **Emergency access**: Maintain emergency access procedures
- **Regular audits**: Review user access quarterly
- **Training**: Ensure users understand security best practices

## 🚨 Common Mistakes to Avoid

- ❌ Sharing user credentials between team members
- ❌ Creating users without MFA requirements
- ❌ Not planning for user lifecycle management
- ❌ Granting excessive permissions "just in case"
- ❌ Not documenting access procedures
- ❌ Forgetting to remove access when people leave
- ❌ Using IAM users for applications (use roles instead)

## 🔗 Official AWS Documentation

### Identity Federation
- [Getting started with IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html)
- [Identity providers and federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers.html)
- [Identity federation in AWS](https://aws.amazon.com/identity/federation/)

### IAM Users
- [Creating IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
- [Temporary security credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html)

## ➡️ What's Next?

Great work! You've established secure access for your team. Now let's configure the right permissions for each user:

**Next Control**: [ACCT.04 - Assign Permissions](./acct-04.md)

---

[← Previous: ACCT.02](./acct-02.md) | [Back to Main Guide](./README.md) | [Next: ACCT.04 →](./acct-04.md)
