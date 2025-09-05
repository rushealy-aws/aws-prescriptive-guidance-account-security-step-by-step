# ACCT.05 - Require Multi-Factor Authentication to Log In

## 🎯 What This Control Does

This control adds an essential security layer by requiring multi-factor authentication (MFA) for all AWS account access. MFA requires users to provide both their password and a code from a separate device, making accounts much harder to compromise.

## 🔍 Why This Matters

- **Prevents account takeover**: Even if passwords are compromised, accounts remain secure
- **Compliance requirement**: Many regulations require MFA for privileged access
- **Industry standard**: MFA is considered essential for cloud security
- **Cost of breach prevention**: Much cheaper than recovering from a security incident
- **Peace of mind**: Sleep better knowing your AWS account is properly protected

## ⏱️ Time Required
**15 minutes per user**

## 📱 MFA Device Options

### Virtual MFA Apps (Recommended for Most Users)
**Pros**: Free, convenient, works on smartphones
**Cons**: Can be lost if phone is lost/broken
**Examples**:
- Google Authenticator
- Microsoft Authenticator
- Authy (supports backup/sync)
- 1Password (if you use 1Password)

### Hardware MFA Devices
**Pros**: Most secure, can't be hacked remotely
**Cons**: Cost money, can be lost/broken
**Examples**:
- YubiKey
- RSA SecurID tokens
- Gemalto tokens

### SMS/Voice (Not Recommended)
**Pros**: No additional device needed
**Cons**: Vulnerable to SIM swapping attacks
**Use case**: Backup method only

---

## 📋 Step-by-Step Instructions

### 🔐 Step 1: Set Up MFA for Root User (Critical!)

#### Download an MFA App
1. Install an authenticator app on your phone:
   - **Google Authenticator** (iOS/Android)
   - **Microsoft Authenticator** (iOS/Android) 
   - **Authy** (iOS/Android) - recommended for backup features

#### Configure Root User MFA
1. **Sign in as root user**:
   - Go to [AWS Management Console](https://console.aws.amazon.com/)
   - Sign in with root user credentials

2. **Access security credentials**:
   - Click your account name in the top-right corner
   - Select **My Security Credentials**
   - If prompted, click **Continue to Security Credentials**

3. **Set up MFA**:
   - Expand the **Multi-Factor Authentication (MFA)** section
   - Click **Activate MFA**
   - Choose **Virtual MFA device**
   - Click **Continue**

4. **Configure your app**:
   - Open your authenticator app
   - Scan the QR code displayed on screen
   - Or manually enter the secret key if you can't scan

5. **Verify setup**:
   - Enter two consecutive MFA codes from your app
   - Click **Assign MFA**
   - You should see "Successfully assigned MFA"

6. **Test immediately**:
   - Sign out and sign back in
   - Verify MFA is required and working

### 👥 Step 2: Set Up MFA for IAM Identity Center (If Using Federation)

#### Enable MFA Requirement
1. **Go to IAM Identity Center**:
   - Navigate to [IAM Identity Center console](https://console.aws.amazon.com/singlesignon)
   - Click **Settings** in the left navigation

2. **Configure MFA settings**:
   - Go to **Multi-factor authentication** tab
   - Click **Configure**
   - Select **Every time they sign in (always-on)**
   - Choose allowed MFA types (recommend: Authenticator apps and Security keys)
   - Click **Save changes**

#### User Setup Process
Each user will need to:
1. Sign in to their AWS access portal
2. Follow the MFA setup wizard
3. Configure their chosen MFA method
4. Test the setup

### 👤 Step 3: Set Up MFA for IAM Users (If Using IAM Users)

#### For Your Own IAM User
1. **Sign in to IAM console**:
   - Go to [IAM console](https://console.aws.amazon.com/iam)
   - Sign in with your IAM user credentials

2. **Access your security credentials**:
   - Click your username in the top-right corner
   - Select **My Security Credentials**

3. **Set up MFA**:
   - In the **Multi-factor authentication** section
   - Click **Manage MFA device**
   - Choose **Virtual MFA device**
   - Follow the same process as for root user

#### For Other IAM Users (As Administrator)
1. **Go to IAM Users**:
   - In IAM console, click **Users**
   - Click on the user's name

2. **Configure MFA**:
   - Go to **Security credentials** tab
   - In **Assigned MFA device** section, click **Manage**
   - Choose **Virtual MFA device**
   - Follow setup process (user will need their phone)

3. **Provide setup instructions**:
   - Give users their MFA setup instructions
   - Ensure they test the setup immediately

## 🔒 Step 4: Enforce MFA with Policies (Advanced)

To require MFA for all actions, create and attach this policy:

### MFA Enforcement Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowViewAccountInfo",
            "Effect": "Allow",
            "Action": [
                "iam:GetAccountPasswordPolicy",
                "iam:ListVirtualMFADevices"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowManageOwnPasswords",
            "Effect": "Allow",
            "Action": [
                "iam:ChangePassword",
                "iam:GetUser"
            ],
            "Resource": "arn:aws:iam::*:user/${aws:username}"
        },
        {
            "Sid": "AllowManageOwnMFA",
            "Effect": "Allow",
            "Action": [
                "iam:CreateVirtualMFADevice",
                "iam:DeleteVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:ListMFADevices",
                "iam:ResyncMFADevice"
            ],
            "Resource": [
                "arn:aws:iam::*:mfa/${aws:username}",
                "arn:aws:iam::*:user/${aws:username}"
            ]
        },
        {
            "Sid": "DenyAllExceptUnlessSignedInWithMFA",
            "Effect": "Deny",
            "NotAction": [
                "iam:CreateVirtualMFADevice",
                "iam:EnableMFADevice",
                "iam:GetUser",
                "iam:ListMFADevices",
                "iam:ListVirtualMFADevices",
                "iam:ResyncMFADevice",
                "sts:GetSessionToken"
            ],
            "Resource": "*",
            "Condition": {
                "BoolIfExists": {
                    "aws:MultiFactorAuthPresent": "false"
                }
            }
        }
    ]
}
```

## ✅ Verification Steps

### Test Each Account Type
1. **Root user**:
   - Sign out and sign back in
   - Verify MFA code is required
   - Test with wrong code to ensure it's rejected

2. **IAM Identity Center users**:
   - Have users sign in through access portal
   - Verify MFA setup is required
   - Test access to AWS console

3. **IAM users**:
   - Each user should sign out and back in
   - Verify MFA is required for console access
   - Test that wrong codes are rejected

### Check MFA Status
1. **Root user**: Check in Security Credentials page
2. **IAM users**: Check in IAM console under each user
3. **Identity Center**: Check MFA compliance in Identity Center console

## 💡 Best Practices

### Device Management
- **Backup codes**: Generate and securely store backup codes
- **Multiple devices**: Consider registering backup MFA devices
- **Device security**: Keep MFA devices secure and updated
- **Lost device procedure**: Have a clear process for lost/broken devices

### Organizational Policies
- **Mandatory MFA**: Make MFA mandatory for all users
- **Regular audits**: Check MFA compliance monthly
- **New user onboarding**: Include MFA setup in onboarding process
- **Emergency access**: Maintain secure emergency access procedures

### Security Considerations
- **Avoid SMS**: Don't use SMS for MFA if possible (SIM swapping risk)
- **Hardware tokens**: Consider hardware tokens for high-privilege users
- **App backup**: Use MFA apps that support secure backup (like Authy)
- **Regular rotation**: Consider rotating MFA devices periodically

## 🚨 Common Mistakes to Avoid

- ❌ Not setting up MFA for root user first
- ❌ Using SMS as primary MFA method
- ❌ Not testing MFA setup immediately after configuration
- ❌ Sharing MFA devices between users
- ❌ Not having a backup plan for lost MFA devices
- ❌ Forgetting to enforce MFA for new users
- ❌ Not documenting MFA recovery procedures
- ❌ Using the same MFA device for multiple critical accounts

## 🆘 Emergency Access Procedures

### If MFA Device is Lost/Broken

#### For Root User:
1. Contact AWS Support immediately
2. Provide account verification information
3. Follow AWS's account recovery process

#### For IAM Users:
1. Administrator can remove MFA requirement temporarily
2. User sets up new MFA device
3. Re-enable MFA requirement

#### For Identity Center Users:
1. Administrator can reset MFA in Identity Center
2. User sets up new MFA device on next login

### Prevention:
- Set up multiple MFA devices when possible
- Keep backup codes in secure location
- Document recovery procedures
- Test recovery procedures regularly

## 🔗 Official AWS Documentation

### General MFA
- [AWS Multi-factor authentication in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html)
- [Using multi-factor authentication (MFA) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_users-self-manage-mfa-and-creds.html)

### Identity Center MFA
- [Enable MFA in IAM Identity Center](https://docs.aws.amazon.com/singlesignon/latest/userguide/mfa-configure.html)

### Hardware Tokens
- [Enabling a hardware MFA device](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_enable_physical.html)

## ➡️ What's Next?

Fantastic! You've significantly improved your account security with MFA. Now let's strengthen password requirements:

**Next Control**: [ACCT.06 - Enforce a Password Policy](./acct-06.md)

---

[← Previous: ACCT.04](./acct-04.md) | [Back to Main Guide](./README.md) | [Next: ACCT.06 →](./acct-06.md)
