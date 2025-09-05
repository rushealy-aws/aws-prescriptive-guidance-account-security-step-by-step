# AWS Security Baseline for New Customers

Welcome! This guide provides a friendly, step-by-step walkthrough of the AWS Prescriptive Guidance for securing your AWS account. Whether you're just starting with AWS or looking to strengthen your security posture, this guide will help you implement essential security controls.

## 🎯 What You'll Accomplish

By following this guide, you'll establish a solid security foundation for your AWS account that includes:

- ✅ Proper account contact management
- ✅ Restricted root user access
- ✅ Multi-factor authentication (MFA)
- ✅ Secure user access management
- ✅ Activity logging and monitoring
- ✅ Cost monitoring and alerting
- ✅ Threat detection capabilities

## 📋 Security Controls Overview

This guide covers 12 essential security controls from the [AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-startup-security-baseline/controls-acct.html):

| Control | Description | Difficulty | Time Required |
|---------|-------------|------------|---------------|
| [ACCT.01](./acct-01.md) | Set account-level contacts to valid email distribution lists | Easy | 10 minutes |
| [ACCT.02](./acct-02.md) | Restrict use of the root user | Easy | 5 minutes |
| [ACCT.03](./acct-03.md) | Configure console access for each user | Medium | 20 minutes |
| [ACCT.04](./acct-04.md) | Assign permissions | Medium | 15 minutes |
| [ACCT.05](./acct-05.md) | Require multi-factor authentication to log in | Easy | 15 minutes |
| [ACCT.06](./acct-06.md) | Enforce a password policy | Easy | 5 minutes |
| [ACCT.07](./acct-07.md) | Deliver CloudTrail logs to a protected S3 bucket | Medium | 25 minutes |
| [ACCT.08](./acct-08.md) | Prevent public access to private S3 buckets | Easy | 10 minutes |
| [ACCT.09](./acct-09.md) | Delete unused VPCs, subnets, and security groups | Easy | 15 minutes |
| [ACCT.10](./acct-10.md) | Configure AWS Budgets to monitor your spending | Easy | 10 minutes |
| [ACCT.11](./acct-11.md) | Enable and respond to GuardDuty notifications | Medium | 20 minutes |
| [ACCT.12](./acct-12.md) | Monitor and resolve high-risk issues using Trusted Advisor | Easy | 10 minutes |

**Total estimated time: 2.5 hours**

## 🚀 Getting Started

### Prerequisites

Before you begin, make sure you have:

- [ ] An AWS account (if you don't have one, [create one here](https://aws.amazon.com/free/))
- [ ] Access to the AWS Management Console
- [ ] Administrative privileges in your AWS account
- [ ] A mobile device or authenticator app for MFA setup

### Recommended Order

While you can implement these controls in any order, we recommend following this sequence for the best experience:

1. **Start with the basics** (ACCT.01, ACCT.02, ACCT.05) - Set up contacts, secure root access, and enable MFA
2. **Configure user access** (ACCT.03, ACCT.04, ACCT.06) - Create proper user management
3. **Enable monitoring** (ACCT.07, ACCT.10, ACCT.11, ACCT.12) - Set up logging and alerting
4. **Clean up and secure** (ACCT.08, ACCT.09) - Remove unnecessary resources and secure S3

## 💡 Tips for Success

- **Take your time**: Security is important - don't rush through the steps
- **Test as you go**: Verify each control is working before moving to the next
- **Keep records**: Document what you've implemented and any customizations you've made
- **Stay updated**: AWS security best practices evolve, so revisit this guide periodically

## 🆘 Need Help?

- **AWS Documentation**: Each control page includes links to official AWS documentation
- **AWS Support**: If you have a support plan, don't hesitate to reach out
- **AWS Community**: The [AWS re:Post community](https://repost.aws/) is a great resource for questions

## 📚 Additional Resources

- [AWS Security Best Practices](https://docs.aws.amazon.com/security/)
- [AWS Well-Architected Security Pillar](https://docs.aws.amazon.com/wellarchitected/latest/security-pillar/)
- [AWS Identity and Access Management Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

**Ready to secure your AWS account?** Start with [ACCT.01 - Set Account-Level Contacts](./acct-01.md) and work your way through each control at your own pace.

*This guide is based on the [AWS Prescriptive Guidance for Startup Security Baseline](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-startup-security-baseline/controls-acct.html). Last updated: September 2024.*
