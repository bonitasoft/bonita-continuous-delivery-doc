# AWS prerequisites

_If it's not already the case, [signup for an AWS account](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#sign-up-for-aws) first._

In order to use Amazon EC2 instances, some configuration steps need to be performed as a prerequisite.
The following steps are required to set up AWS credentials for Ansible automation.

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
1. [Create an IAM Group](http://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups_create.html)
    - **Group name**: bonita-provisioning
    - Select the IAM Policies created previously
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

Bonita Provisioning solution uses official Ubuntu cloud images as EC2 AMI (Amazon Machine Images).
Use the [Amazon EC2 AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/) tools to identify IDs of such images.

## Configuration related to the location of your BCD Controller

The `ec2_wrapper.sh` inventory script can be further configured in `templates/ec2.ini.j2`.
For instance the `vpc_destination_variable` parameter can be set to `public_dns_name` (in order to use Ansible from outside EC2) through the Ansible variable named `ec2_vpc_destination_variable`.
More information can be found in this [blog post](https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/).
