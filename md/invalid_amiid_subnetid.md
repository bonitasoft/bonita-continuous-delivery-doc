# InvalidAMIID.NotFound and InvalidSubnetID.NotFound

iIf while you launch a `create` command in order to create instances on AWS you get errors like these:

```
[...]
InvalidAMIID.NotFound: The image id '[ami-fc4f5e85]' does not exist
[...]
Error launching source instance: InvalidSubnetID.NotFound: The subnet ID 'subnet-ab123456' does not exist
[...]
```

You should check that `ec2_subnet_ids` and `aws_ami` correspond to the region set into `ec2_region`.
