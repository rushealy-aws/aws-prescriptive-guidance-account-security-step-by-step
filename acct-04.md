# ACCT.04 - Assign Permissions

## 🎯 What This Control Does

This control establishes proper permission management by assigning the right level of access to each user based on their job function. It follows the principle of least privilege - giving users only the permissions they need to do their job.

## 🔍 Why This Matters

- **Security**: Limits potential damage from compromised accounts
- **Compliance**: Meets regulatory requirements for access control
- **Operational safety**: Prevents accidental changes to critical resources
- **Audit readiness**: Clear permission structure for security reviews
- **Cost control**: Prevents unauthorized resource creation

## ⏱️ Time Required
**15 minutes**

## 🎭 Understanding AWS Permissions

### Key Concepts
- **Policies**: Documents that define permissions (what actions are allowed/denied)
- **Roles**: Temporary credentials that can be assumed
- **Groups**: Collections of users with similar permission needs
- **Least Privilege**: Giving minimum permissions necessary for the job

### AWS Managed Policies (Recommended Starting Point)
AWS provides pre-built policies for common job functions:

| Policy Name | Best For | Access Level |
|-------------|----------|--------------|
| `AdministratorAccess` | System administrators | Full access to all AWS services |
| `PowerUserAccess` | Developers, DevOps engineers | Full access except IAM/Organizations |
| `ReadOnlyAccess` | Auditors, managers | View-only access to all services |
| `DatabaseAdministrator` | Database administrators | Full access to database services |
| `NetworkAdministrator` | Network engineers | Full access to networking services |
| `SecurityAudit` | Security team | Read access for security auditing |

---

## 📋 Step-by-Step Instructions

### For Identity Federation (IAM Identity Center)

#### Step 1: Create Permission Sets

1. **Navigate to IAM Identity Center**:
   - Go to [IAM Identity Center console](https://console.aws.amazon.com/singlesignon)
   - Click **Permission sets** in the left navigation

2. **Create a new permission set**:
   - Click **Create permission set**
   - Choose **Predefined permission set** for AWS managed policies
   - Or choose **Custom permission set** for specific needs

3. **Configure the permission set**:
   - **Name**: Use descriptive names like "Developers" or "ReadOnly-Auditors"
   - **Description**: Explain what this permission set is for
   - **Session duration**: Set appropriate timeout (1-12 hours)

4. **Select policies**:
   - Choose appropriate AWS managed policies
   - For custom needs, attach additional policies

#### Step 2: Assign Permission Sets to Users

1. **Go to AWS accounts**:
   - In IAM Identity Center, click **AWS accounts**
   - Select your account

2. **Assign users or groups**:
   - Click **Assign users or groups**
   - Select the users or groups
   - Choose the appropriate permission set
   - Click **Submit**

### For IAM Users

#### Step 1: Create User Groups

1. **Navigate to IAM Groups**:
   - Go to [IAM console](https://console.aws.amazon.com/iam)
   - Click **User groups** in the left navigation
   - Click **Create group**

2. **Configure the group**:
   - **Group name**: Use job function names (e.g., "Developers", "Admins", "ReadOnly")
   - **Attach permissions policies**: Select appropriate AWS managed policies
   - Click **Create group**

3. **Repeat for each role type** in your organization

#### Step 2: Add Users to Groups

1. **Select a user group**:
   - Click on the group name
   - Go to the **Users** tab
   - Click **Add users to group**

2. **Select users**:
   - Check the boxes for appropriate users
   - Click **Add users**

## 🎯 Common Permission Patterns

### Small Development Team
```
👥 Administrators (2-3 people)
   └── AdministratorAccess

👥 Developers (3-10 people)
   └── PowerUserAccess

👥 Read-Only (managers, auditors)
   └── ReadOnlyAccess
```

### Larger Organization
```
👥 System Administrators
   └── AdministratorAccess

👥 DevOps Engineers
   └── PowerUserAccess
   └── Custom policies for CI/CD

👥 Application Developers
   └── Custom policy (EC2, S3, Lambda, RDS)

👥 Database Administrators
   └── DatabaseAdministrator

👥 Security Team
   └── SecurityAudit
   └── Custom security policies

👥 Finance/Management
   └── ReadOnlyAccess
   └── Billing access policies
```

## 🛠️ Creating Custom Policies (Advanced)

If AWS managed policies don't meet your needs:

### Step 1: Identify Required Permissions
- List the specific AWS services users need
- Determine what actions they need (read, write, delete)
- Identify any resources they should NOT access

### Step 2: Create Custom Policy
1. In IAM console, go to **Policies**
2. Click **Create policy**
3. Use the visual editor or JSON editor
4. Test with a non-production user first

### Example Custom Policy (Developer with Limited Access)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:*",
                "s3:*",
                "lambda:*",
                "logs:*",
                "cloudformation:*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ec2:TerminateInstances",
                "s3:DeleteBucket"
            ],
            "Resource": "*"
        }
    ]
}
```

## ✅ Verification Steps

1. **Test user access**:
   - Have each user sign in and test their permissions
   - Verify they can access what they need
   - Confirm they cannot access restricted resources

2. **Review permission assignments**:
   - Check that users are in appropriate groups
   - Verify no users have excessive permissions
   - Confirm no users are missing necessary access

3. **Document the structure**:
   - Create a document showing who has what access
   - Include justification for permission levels
   - Plan regular review schedule

## 💡 Best Practices

### Permission Management
- **Start restrictive**: Begin with minimal permissions and add as needed
- **Regular reviews**: Audit permissions quarterly
- **Job-based groups**: Organize by job function, not individual needs
- **Document decisions**: Keep records of why specific permissions were granted

### AWS Managed Policies
- **Prefer managed policies**: Use AWS managed policies when possible
- **Understand implications**: Know what each managed policy allows
- **Monitor changes**: AWS updates managed policies - stay informed
- **Combine carefully**: Multiple policies can have unexpected interactions

### Custom Policies
- **Test thoroughly**: Always test custom policies in non-production first
- **Use conditions**: Add conditions to limit when/where policies apply
- **Version control**: Keep custom policies in version control
- **Regular updates**: Review and update custom policies regularly

## 🚨 Common Mistakes to Avoid

- ❌ Giving everyone `AdministratorAccess` "to make things easier"
- ❌ Creating individual policies for each user instead of using groups
- ❌ Not testing permissions before deploying to production
- ❌ Forgetting to remove permissions when people change roles
- ❌ Using overly complex custom policies when managed policies would work
- ❌ Not documenting permission decisions
- ❌ Granting permissions "just in case" without clear justification

## 🔗 Official AWS Documentation

### General IAM
- [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
- [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html)
- [Granting least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)

### Identity Center
- [Use IAM policies in permission sets](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocmp.html)

### IAM Users and Groups
- [Creating IAM user groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups_create.html)
- [Adding IAM identity permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-attach-detach.html)

## ➡️ What's Next?

Excellent! You've established proper permission management. Now let's add an essential security layer with multi-factor authentication:

**Next Control**: [ACCT.05 - Require Multi-Factor Authentication](./acct-05.md)

---

[← Previous: ACCT.03](./acct-03.md) | [Back to Main Guide](./README.md) | [Next: ACCT.05 →](./acct-05.md)
