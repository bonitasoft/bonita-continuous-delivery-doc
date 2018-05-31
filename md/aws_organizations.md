# Assume IAM role within AWS Organizations

[AWS Organizations](https://aws.amazon.com/organizations) offers policy-based management for multiple AWS accounts.
If you use an Organization with IAM users (for SSO, see [Single Sign-On to AWS Using G Suite](aws_sso.md)), you will have to assume a specific role on the target account. To do so you will still put your `aws_access_key_id` and `aws_secret_access_key` into the `.boto` or `.aws/credentials` file.
Then you will just have to modify the scenario in order to set the corresponding role arn, for example :

```
aws_assumed_iam_role: arn:aws:iam::123456789012:role/BCD
```

Note:
- `123456789012` represents here the target account
- `BCD` represents here the role on this account

It implies that you should have created the corresponding role with proper rights. To do so you just have to follow the [How to configure Amazon Web Services (AWS) for BCD](aws_prerequisites.md) and in `AWS Setup for BCD` section replace the point 5 `Create an IAM Group` by a [creation of the corresponding role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) attached to the IAM Policies created previously.
