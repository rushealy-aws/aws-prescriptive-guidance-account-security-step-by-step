# ACCT.09 - Delete Unused VPCs, Subnets, and Security Groups

## 🎯 What This Control Does

This control removes unused networking resources that AWS creates by default in every region. These default resources can create security risks by providing unnecessary network access points and can complicate your network security posture.

## 🔍 Why This Matters

- **Reduced attack surface**: Fewer network entry points for potential attackers
- **Simplified security**: Easier to manage and monitor your actual network resources
- **Cost optimization**: Eliminates charges for unused NAT gateways and other resources
- **Compliance**: Cleaner environment for security audits and compliance reviews
- **Operational clarity**: Focus on resources you actually use

## ⏱️ Time Required
**15 minutes**

## 🌐 Understanding Default AWS Networking

### What AWS Creates by Default
When you create an AWS account, AWS automatically creates:
- **Default VPC** in every region
- **Default subnets** in each Availability Zone
- **Default security group** in each VPC
- **Default route table** and **internet gateway**

### Why Remove Them?
- **Overly permissive**: Default security groups allow all traffic between instances
- **Unnecessary exposure**: Public subnets in regions you don't use
- **Complexity**: Makes it harder to understand your actual network topology
- **Compliance issues**: May violate security policies requiring explicit network design

---

## 📋 Step-by-Step Instructions

### Step 1: Inventory Your Current Resources

#### Use EC2 Global View (Recommended)
1. **Navigate to EC2 Global View**:
   - Go to [EC2 Global View console](https://console.aws.amazon.com/ec2globalview/home)
   - This shows resources across all regions in one view

2. **Review VPCs across all regions**:
   - Look for VPCs marked as "default"
   - Note which regions you actually use
   - Identify regions with no resources except defaults

#### Manual Region-by-Region Check
1. **Switch between regions**:
   - Use the region selector in the top-right corner
   - Check each region systematically

2. **For each region, check**:
   - VPCs (look for "default" in the name)
   - Running EC2 instances
   - Load balancers
   - RDS instances
   - Other resources that depend on VPCs

### Step 2: Identify Safe-to-Delete Resources

#### Regions to Clean Up
Focus on regions where you have:
- ✅ No running EC2 instances
- ✅ No RDS databases
- ✅ No load balancers
- ✅ No Lambda functions in VPC
- ✅ No other VPC-dependent resources

#### Resources to Keep
**DO NOT DELETE** default VPCs in regions where you have:
- ❌ Any running instances
- ❌ RDS databases
- ❌ Load balancers
- ❌ NAT gateways
- ❌ VPC endpoints
- ❌ Any resources you're actively using

### Step 3: Delete Default VPCs (Safe Regions Only)

⚠️ **Warning**: Deleting a VPC also deletes all its subnets, route tables, and security groups. Make sure you have no resources in the VPC first!

#### For Each Unused Region:

1. **Switch to the target region**:
   - Select the region in the AWS console

2. **Navigate to VPC console**:
   - Go to [VPC console](https://console.aws.amazon.com/vpc)

3. **Verify no dependencies**:
   - Check **Instances** - should be empty
   - Check **Load Balancers** - should be empty
   - Check **NAT Gateways** - should be empty
   - Check **VPC Endpoints** - should be empty

4. **Delete the default VPC**:
   - Go to **Your VPCs**
   - Select the default VPC (usually named "default")
   - Click **Actions** → **Delete VPC**
   - Type `delete` to confirm
   - Click **Delete**

5. **Verify deletion**:
   - Confirm the VPC and all its components are removed
   - Check that no orphaned resources remain

### Step 4: Clean Up Unused Security Groups

In regions where you keep VPCs but have unused security groups:

1. **Navigate to Security Groups**:
   - In EC2 console, go to **Security Groups**

2. **Identify unused groups**:
   - Look for groups with no associated instances
   - Check the **Description** tab for each group
   - Note: You cannot delete the default security group

3. **Delete unused groups**:
   - Select unused security groups
   - Click **Actions** → **Delete security groups**
   - Confirm deletion

### Step 5: Document Your Network Architecture

1. **Create a network inventory**:
   - List regions you use
   - Document VPCs you've kept and why
   - Note any custom networking resources

2. **Update your architecture documentation**:
   - Include network diagrams
   - Document security group purposes
   - Plan for future network needs

## ✅ Verification Steps

### Confirm Successful Cleanup
1. **Use EC2 Global View**:
   - Verify unused regions show no VPCs
   - Confirm active regions only have needed resources

2. **Test region access**:
   - Try launching an instance in a cleaned region
   - Verify you get prompted to create a VPC (expected behavior)

3. **Check for orphaned resources**:
   - Look for security groups with no VPC
   - Check for route tables without VPCs
   - Verify no unexpected charges appear

### Document Current State
1. **Create a "regions in use" list**
2. **Document remaining VPCs and their purposes**
3. **Note any custom networking configurations**

## 💡 Best Practices

### Before Deletion
- **Double-check dependencies**: Use AWS Config or CLI to find all resources
- **Take screenshots**: Document current state before changes
- **Start with one region**: Test the process in a clearly unused region first
- **Coordinate with team**: Ensure no one is planning to use these resources

### Network Design
- **Explicit VPC creation**: Create VPCs intentionally when needed
- **Regional strategy**: Only use regions you actually need
- **Security group naming**: Use descriptive names for security groups
- **Documentation**: Maintain current network architecture documentation

### Ongoing Management
- **Regular cleanup**: Review unused resources quarterly
- **New region policy**: Establish process for using new regions
- **Monitoring**: Set up alerts for new VPC creation
- **Cost tracking**: Monitor networking costs by region

## 🚨 Common Mistakes to Avoid

- ❌ Deleting VPCs with running instances (will fail, but causes confusion)
- ❌ Not checking for Lambda functions in VPCs
- ❌ Forgetting about RDS instances in private subnets
- ❌ Deleting VPCs in regions you plan to use soon
- ❌ Not documenting what you've deleted
- ❌ Not coordinating with team members
- ❌ Rushing through multiple regions without careful checking

## 🔧 Advanced Cleanup with AWS CLI

### List All VPCs Across Regions
```bash
# List all regions
aws ec2 describe-regions --query 'Regions[].RegionName' --output text

# Check VPCs in each region
for region in $(aws ec2 describe-regions --query 'Regions[].RegionName' --output text); do
    echo "Region: $region"
    aws ec2 describe-vpcs --region $region --query 'Vpcs[?IsDefault==`true`].[VpcId,State]' --output table
done
```

### Delete Default VPC via CLI
```bash
# Get default VPC ID
VPC_ID=$(aws ec2 describe-vpcs --region us-west-2 --filters "Name=isDefault,Values=true" --query 'Vpcs[0].VpcId' --output text)

# Delete default VPC (this deletes subnets, route tables, etc.)
aws ec2 delete-vpc --region us-west-2 --vpc-id $VPC_ID
```

### Check for Dependencies
```bash
# Check for instances in VPC
aws ec2 describe-instances --region us-west-2 --filters "Name=vpc-id,Values=$VPC_ID" --query 'Reservations[].Instances[].InstanceId'

# Check for load balancers
aws elbv2 describe-load-balancers --region us-west-2 --query 'LoadBalancers[?VpcId==`'$VPC_ID'`].LoadBalancerName'

# Check for RDS instances
aws rds describe-db-instances --region us-west-2 --query 'DBInstances[?DBSubnetGroup.VpcId==`'$VPC_ID'`].DBInstanceIdentifier'
```

## 📊 Monitoring and Automation

### AWS Config Rules
Set up rules to monitor network resources:
```json
{
    "ConfigRuleName": "vpc-default-security-group-closed",
    "Source": {
        "Owner": "AWS",
        "SourceIdentifier": "VPC_DEFAULT_SECURITY_GROUP_CLOSED"
    }
}
```

### CloudWatch Metrics
Monitor for unexpected resource creation:
- VPC creation events in CloudTrail
- Security group creation in unused regions
- Instance launches in default VPCs

## 🔗 Official AWS Documentation

- [Delete your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/delete-vpc.html)
- [List and filter resources across Regions using Amazon EC2 Global View](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Filtering.html#global-view)
- [Default VPCs and default subnets](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html)

## ➡️ What's Next?

Great job cleaning up your network resources! Now let's set up cost monitoring to keep track of your AWS spending:

**Next Control**: [ACCT.10 - Configure AWS Budgets to Monitor Your Spending](./acct-10.md)

---

[← Previous: ACCT.08](./acct-08.md) | [Back to Main Guide](./README.md) | [Next: ACCT.10 →](./acct-10.md)
