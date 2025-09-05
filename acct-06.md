# ACCT.06 - Enforce a Password Policy

## 🎯 What This Control Does

This control establishes strong password requirements for IAM users to prevent password-based attacks. A good password policy makes it much harder for attackers to guess or crack passwords through brute force or dictionary attacks.

## 🔍 Why This Matters

- **Prevents brute force attacks**: Strong passwords are much harder to crack
- **Reduces credential stuffing**: Unique passwords prevent reuse from other breaches
- **Compliance requirement**: Many standards require strong password policies
- **Defense in depth**: Additional security layer even with MFA enabled
- **User behavior**: Encourages good password habits across your organization

## ⏱️ Time Required
**5 minutes**

## 🔐 Modern Password Policy Best Practices

### Current Recommendations (2024)
Based on NIST guidelines and security research:

✅ **DO**:
- Require minimum 12-14 characters
- Allow all printable characters (including spaces and emojis)
- Check against known breached passwords
- Require change only when compromised
- Allow password managers and copy/paste

❌ **DON'T**:
- Force regular password changes (unless compromised)
- Require complex character combinations
- Prohibit common words (if length is sufficient)
- Limit password length unnecessarily

### AWS Password Policy Options
AWS allows you to configure:
- Minimum password length (6-128 characters)
- Character requirements (uppercase, lowercase, numbers, symbols)
- Password expiration
- Password reuse prevention
- Account lockout settings

---

## 📋 Step-by-Step Instructions

### Step 1: Access IAM Account Settings

1. **Sign in to IAM Console**:
   - Go to [IAM console](https://console.aws.amazon.com/iam)
   - Sign in as an administrator

2. **Navigate to Account Settings**:
   - In the left navigation pane, click **Account settings**
   - You'll see the current password policy (if any)

### Step 2: Configure Password Policy

1. **Edit Password Policy**:
   - In the **Password policy** section, click **Change password policy**

2. **Configure Basic Requirements**:
   ```
   ✅ Minimum password length: 14 characters
   ✅ Require at least one uppercase letter
   ✅ Require at least one lowercase letter  
   ✅ Require at least one number
   ✅ Require at least one non-alphanumeric character
   ```

3. **Configure Advanced Settings**:
   ```
   ✅ Allow users to change their own password
   ❌ Password expires (leave unchecked for modern approach)
   ✅ Prevent password reuse: 12 passwords
   ✅ Account lockout threshold: 5 attempts
   ✅ Account lockout period: 30 minutes
   ```

### Step 3: Apply Recommended Settings

Here's a balanced approach that follows modern security guidelines:

#### Recommended Configuration:
```
Minimum password length: 14 characters
Require uppercase letters: Yes
Require lowercase letters: Yes  
Require numbers: Yes
Require symbols: Yes
Allow users to change password: Yes
Password expires: No (modern approach)
Prevent password reuse: 12 passwords
Account lockout: 5 failed attempts
Lockout duration: 30 minutes
```

#### Alternative (High Security) Configuration:
```
Minimum password length: 16 characters
Require uppercase letters: Yes
Require lowercase letters: Yes
Require numbers: Yes
Require symbols: Yes
Allow users to change password: Yes
Password expires: No
Prevent password reuse: 24 passwords
Account lockout: 3 failed attempts
Lockout duration: 60 minutes
```

### Step 4: Save and Test

1. **Save Changes**:
   - Review your settings
   - Click **Save changes**

2. **Test the Policy**:
   - Try creating a test user with a weak password
   - Verify the policy blocks weak passwords
   - Test with a compliant password to ensure it works

## ✅ Verification Steps

### Test Password Requirements
1. **Create a test user**:
   - Try setting a password that violates each rule
   - Verify each violation is caught and blocked

2. **Test user experience**:
   - Have existing users change their passwords
   - Ensure they understand the new requirements
   - Verify password managers work properly

3. **Check policy display**:
   - Confirm users can see password requirements
   - Verify error messages are clear and helpful

## 💡 Best Practices

### Password Policy Design
- **Balance security and usability**: Don't make passwords so complex users write them down
- **Educate users**: Explain why strong passwords matter
- **Promote password managers**: Encourage use of password management tools
- **Regular review**: Reassess policy effectiveness annually

### User Communication
- **Advance notice**: Give users warning before implementing new policies
- **Clear instructions**: Provide examples of compliant passwords
- **Support resources**: Offer help for users struggling with new requirements
- **Password manager recommendations**: Suggest specific tools

### Organizational Considerations
- **Consistent policies**: Align with your organization's other password policies
- **Compliance requirements**: Ensure policy meets regulatory needs
- **Emergency procedures**: Plan for users locked out of accounts
- **Monitoring**: Track policy violations and user feedback

## 🚨 Common Mistakes to Avoid

- ❌ Making passwords so complex users can't remember them
- ❌ Forcing frequent password changes (creates weaker passwords)
- ❌ Not allowing password managers or copy/paste
- ❌ Setting lockout periods too long (impacts productivity)
- ❌ Not communicating changes to users in advance
- ❌ Not providing examples of compliant passwords
- ❌ Ignoring user feedback about policy usability
- ❌ Not having unlock procedures for locked accounts

## 🔧 Advanced Configuration

### Custom Password Policy via CLI/API
For more advanced requirements, you can use AWS CLI:

```bash
aws iam update-account-password-policy \
    --minimum-password-length 14 \
    --require-symbols \
    --require-numbers \
    --require-uppercase-characters \
    --require-lowercase-characters \
    --allow-users-to-change-password \
    --max-password-age 0 \
    --password-reuse-prevention 12 \
    --hard-expiry false
```

### Integration with External Systems
- **Active Directory**: Sync policies with on-premises AD
- **Identity providers**: Ensure federated users have similar requirements
- **Password managers**: Test compatibility with organizational tools

## 📚 User Education Materials

### Sample User Communication
```
Subject: New AWS Password Policy - Action Required

Dear Team,

We're implementing a stronger password policy for AWS access to better protect our systems. Starting [DATE], all AWS passwords must:

✅ Be at least 14 characters long
✅ Include uppercase and lowercase letters
✅ Include at least one number
✅ Include at least one symbol (!@#$%^&*)

Tips for creating strong passwords:
• Use a passphrase: "Coffee!Keeps@Me#Awake2024"
• Use a password manager (recommended: 1Password, Bitwarden)
• Avoid common words or personal information

Need help? Contact IT support at [CONTACT INFO]
```

### Password Examples
**Good passwords**:
- `MyDog$Loves2Run!InThe@Park`
- `Coffee&Code=Perfect#Day2024`
- `I@Love$AWS#Security!123`

**Bad passwords**:
- `password123` (too simple)
- `Password!` (too short)
- `MyCompanyName2024` (predictable)

## 🔗 Official AWS Documentation

- [Setting an account password policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_passwords_account-policy.html)
- [Password Policy Guide (CIS)](https://www.cisecurity.org/insights/white-papers/cis-password-policy-guide)
- [NIST Password Guidelines](https://pages.nist.gov/800-63-3/sp800-63b.html)

## ➡️ What's Next?

Great work! You've established strong password requirements. Now let's set up comprehensive activity logging:

**Next Control**: [ACCT.07 - Deliver CloudTrail Logs to a Protected S3 Bucket](./acct-07.md)

---

[← Previous: ACCT.05](./acct-05.md) | [Back to Main Guide](./README.md) | [Next: ACCT.07 →](./acct-07.md)
