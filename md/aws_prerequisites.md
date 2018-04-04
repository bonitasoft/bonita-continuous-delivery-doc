# How to configure Amazon Web Services (AWS) for BCD

This tutorial describes how to configure Amazon Web Services (AWS) for BCD.  
These configuration steps are required to allow BCD to programmatically manage your AWS instances and to be able to connect to your Bonita stack.


## Sign Up for AWS

If you do not have an Amazon Web Services account yet, first sign up as described in this AWS user guide: [Sign Up for AWS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#sign-up-for-aws).


## AWS Setup for BCD

In order to use Amazon EC2 instances, some configuration steps need to be performed as a prerequisite.  
The following steps are the basic requirements to set up AWS credentials for Ansible automation.  
Further configuration instructions for **AWS single sign-on** can be found [on this page](aws_sso.md).

1. [Create an IAM Policy](http://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) to grant full access to Amazon EC2 on a specific region (eg. us-west-2)
    - **Policy name**: EC2FullAccess_us-west-2
    - **Policy document**:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": "ec2:*",
                "Effect": "Allow",
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "ec2:Region": "us-west-2"
                    }
                }
            }
        ]
    }
    ```
1. Create an IAM Policy to allow to describe DB instances and list tags (mandatory for dynamic inventory script).
    - **Policy name**: RDSDescribeDBInstances
    - **Policy document**:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "Stmt1498730873000",
                "Effect": "Allow",
                "Action": [
                    "rds:DescribeDBInstances",
                    "rds:ListTagsForResource"
                ],
                "Resource": [
                    "*"
                ]
            }
        ]
    }
    ```
1. Create an IAM Policy to grant full access to Amazon RDS on a specific region (eg. us-west-2). In order to do it you will need your account id (eg. 012345678901)
    - **Policy name**: RDSFullAccess_us-west-2
    - **Policy document**:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "rds:*"
                ],
                "Effect": "Allow",
                "Resource": "arn:aws:rds:us-west-2:012345678901:*"
            }
        ]
    }
    ```    
1. Create an IAM Policy to give [PassRole](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) permission in order to be able to associate a specific role with the EC2 instances of a cluster.
    - **Policy name**: IAMPassRole
    - **Policy document**:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": "iam:PassRole",
                "Effect": "Allow",
                "Resource": "*"
            }
        ]
    }
    ```
1. [Create an IAM Group](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups_create.html)
    - **Group name**: bonita-provisioning
    - Select the IAM Policies created previously
1. Create an IAM Policy to allow ec2 instances of a cluster to autodiscover themselves on a specific region (eg. us-west-2).
    - **Policy name**: ClusterBCD_us-west-2
    - **Policy document**:
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "ec2:DescribeInstances"
                ],
                "Effect": "Allow",
                "Resource": "*",
                "Condition": {
                    "StringEquals": {
                        "ec2:Region": "us-west-2"
                    }
                }
            }
        ]
    }
    ```
    ::: warning
    :fa-exclamation-triangle: Pay attention that for security reasons, pushing AWS user credentials to EC2 instances (as done with BCD 1.0.x) is no longer supported.
    :::
1. [Create an IAM Role for an AWS service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)
    - Select EC2 to "Allows EC2 instances to call AWS services on your behalf."
    - Select the IAM Policy created previously (ClusterBCD_us-west-2)
    - **Role name**: ClusterBCD
1. [Create an IAM User](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)
    - **Access type**: Programmatic access
    - Add user to the IAM group created previously
    - Download as .csv (or copy) Access key information (**Access key ID** and **Secret access key**) for later use
1. Now let's assume you are using an EC2 region which supports the [EC2-VPC platform](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-platforms.html).
Therefore a [default VPC and default subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html) should already be available.
1. [Create an EC2 Security Group](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html#creating-security-group)
    - **Security group name**: sg_bonita-provisioning
    - Add an Inbound rule to allow communication between Bonita stack components
      - **Type**: All Traffic, **Source**: &lt;security_group_id_of_sg_bonita-provisioning&gt;
    - Add an Inbound rule to allow Ansible to connect via SSH
      - **Type**: SSH, **Source**: My IP
    - Add an Inbound rule to allow remote connection to Bonita Tomcat via HTTP
      - **Type**: Custom TCP Rule, **Port Range**: 8081, **Source**: My IP
    - Add an Inbound rule to allow remote connection to Bonita databases
      - **Type**: Custom TCP Rule, **Source**: My IP
        - with `bonita_db_vendor: postgres` - **Port Range**: 5432
        - with `bonita_db_vendor: mysql` - **Port Range**: 3306
        - with `bonita_db_vendor: oracle` - **Port Range**: 1521
1. [Create an EC2 Key Pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
    - _Note: A key pair is linked to an AWS region_
    - Download the `.pem` private key file
    - Ensure private key file permissions: `$ chmod 400 <keyfile_basename>.pem`

BCD is compatible with official Ubuntu cloud images as EC2 AMI (Amazon Machine Images).  
Use the [Amazon EC2 AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/) tools to identify IDs of such images.


## Dynamic EC2 inventory configuration

When deploying to AWS, BCD uses [dynamic Amazon EC2 inventory](http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html#example-aws-ec2-external-inventory-script) with a `ec2_wrapper.sh` script.

This `ec2_wrapper.sh` script can be further configured with the `ec2.ini.j2` template.

For instance, if you are running the BCD controller **from outside EC2**, the `vpc_destination_variable` parameter should be set to `ip_address`. If you are running the BCD controller **from within EC2**, the `vpc_destination_variable` parameter should be set to `private_ip_address`.  
This parameter is managed by the `ec2_vpc_destination_variable` BCD variable.

More information on dynamic EC2 inventory management can be found in [this blog post](https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/).
