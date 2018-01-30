# AWS Organizations

[AWS Organizations](https://aws.amazon.com/organizations) offers policy-based management for multiple AWS accounts.
If you use an Organization with IAM users (for SSO, see [Single Sign-On to AWS Using G Suite](aws_sso_using_gsuite.md)), you will have to assume a specific role on the target account. To do so you will still put your `aws_access_key_id` and `aws_secret_access_key` into the boto file.
Then you will just have to modify the scenario in order to set the corresponding role arn, for example :

```
aws_iam_role: arn:aws:iam::123456789012:role/BCD
```
