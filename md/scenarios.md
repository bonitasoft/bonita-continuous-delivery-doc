# BCD Scenario reference

Scenarios enable you to define your infrastructure and Bonita stack. A scenario is a YAML Ansible variable file that BCD injects to the underlying Ansible playbooks. Its syntax basically follows [Ansible's YAML syntax](http://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html).

The variables described in a scenario have precedence over those defined in the playbooks or the group vars. It is still possible to overload scenario variables with `--extra-vars` declaration in the `bcd` command line. For instance overloading `bonita_version` can be interesting if you want to test multiple versions of Bonita without duplicating scenario files.

::: info
An example scenario file named **uswest_performance.yml.EXAMPLE** is provided in the **scenarios** directory. Use this example to customize your scenario and save it with a `.yml` file extension.
:::

## Core variables

These are general variables used while provisioning your platform.

|Name|Mandatory|Default|Description|Sample|
|--- |--- |--- |--- |--- |
|bcd_provider|Y|Infrastructure provider. Choose between `aws` and `on-premises`. With `aws` provider, BCD may create and destroy EC2 instances of the Bonita stack.|aws|
|bcd_stack_id|Y|Bonita stack identifier used to group machines by stack membership. For AWS deployments the characters allowed are limited to letters, numbers and underscore.|uswest2_test|
|bcd_inventory|N|Ansible inventory path required when `bcd_provider` is `on-premises`. Example on-premises inventories are provided in the `ansible/inventory/vagrant` directory.|ansible/inventory/vagrant/1-machine/inventory|
|bonita_on_single_host|N|Set to `True` when all containers of the Bonita stack is deployed on a single host.|False|
|configuration|Y|Configuration filename used to group variables for common deployment types. The corresponding YAML file must exist in the `ansible/vars` directory.|aws_vars|
|ansible_ssh_user|N|ubuntu|SSH user|ubuntu|
|ansible_private_key_file|Y|Path to the private SSH key used to connect to target hosts.|~/.ssh/jenkins_ansible_us-west-2.pem|


## AWS provider variables

The following variables are specific to AWS deployments ie. when `bcd_provider` is set to `aws`.

|Name|Mandatory|Default|Description|Sample|
|--- |--- |--- |--- |--- |
|ec2_region|Y|AWS region.|us-west-2|
|ec2_security_group|Y|Security group id for the target instances|sg-944888f1|
|ec2_security_group_name|Y|Security group name for the target instances|default|
|ec2_subnet_ids|Y|Subnet id for the target instances|subnet-aa14e0cf|
|ec2_keypair|Y|Key pair name for the target instances. Linked to ansible_private_key_file.|jenkins_ansible_us-west-2|
|ec2_discovery_iam_role|N|Name of the IAM role to attach to EC2 instances to allow Hazelcast EC2 auto-discovery. This is MANDATORY when deploying a Bonita cluster on AWS with BCD.|ClusterBCD|
|ec2_vpc_destination_variable|N|Type of network address the EC2 dynamic inventory script should use. For instance, if you are running the BCD controller **from outside EC2**, the `vpc_destination_variable` parameter should be set to `ip_address`. If you are running the BCD controller **from within EC2**, the `vpc_destination_variable` parameter should be set to `private_ip_address`.|ip_address|
|boto_path|N|~/.boto|Path to the Boto file with the AWS credentials.|
|rds|N|false|Use an existing AWS RDS database instance. By default this parameter is set to `false` to create an EC2 instance for Bonita database.|true|
|aws_ami|Y|Common AMI for all EC2 instances. Depends on AWS region. Should be Ubuntu 14.04 (trusty) and ebs-ssd type.|ami-fc4f5e85|
|aws_database_instance_type|Y|EC2 instance type for the database instance.|t2.micro|
|aws_bonita_instance_type|Y|EC2 instance type for the Bonita instances.|t2.micro|
|aws_load_balancer_instance_type|Y|EC2 instance type for the load balancer instance.|t2.micro|
|aws_assumed_iam_role|N| |An IAM role can be assumed, so all requests are run as that role. This can be useful for connecting across different accounts, or to limit user access. Set this parameter if you want to assume an IAM role while deploying on AWS. It is useful in an AWS organization with IAM users and multiple AWS accounts. See [AWS Organizations](aws_organizations.md)|arn:aws:iam::123456789012:role/BCD|


## Bonita variables

The following parameters are specific to the Bonita instances.

|Name|Mandatory|Default|Description|Sample|
|--- |--- |--- |--- |--- |
|bonita_version|Y|Bonita version. Used for the Docker tar.gz filename|7.5.0|
|bonita_edition|N|yes|performance edition of Bonita by default, other values: teamwork, efficiency, community. Docker tar filename depends on this.|performance|
|bonita_cluster_mode|N|false|Activates cluster mode for Bonita|false|
|bonita_cluster_qty|N|1|Number of instances for Bonita. This variable is also used in EC2 mode for the number of Bonita EC2 instance.|1|
|bonita_port|N|8081|Bonita HTTP port|8081|
|bonita_db_vendor|N|postgres|Database vendor (postgres, mysql, oracle)|postgres|
|bonita_db_host|N|Database host (usually retrieved from inventory)|postgres.a1bac2defghi.us-west-2.rds.amazonaws.com|
|bonita_db_port|N|5432|Database port|5432|
|bonita_db_admin_user|N|postgres|Admin user on database instance|root|
|bonita_db_admin_pass|N|mysecretpassword|Admin password on database instance|password|
|bonita_http_api|N|false|Activates the Bonita [HTTP API](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc9)|true|
|bonita_rest_api_dyn_auth_checks|N|true|Activates [dynamic authorization checking](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc2) on REST API|false|
|bonita_published_ports_extra|N|[]|A list of additional host-to-container port mappings (`<host_port>:<container_port>`) to publish|[ '9020:9010', '2222:1111' ]|


## Licensing variables

In order that BCD retrieves Bonita licenses during the deployment of your platform, you need to set the following variables in your scenario:

::: info
These information are provided by Bonitasoft when you purchase the BCD add-on. Contact your sales representative for further details.
:::

|Name|Description|Sample|
|--- |--- |--- |
|lic_ws_login|Login to Bonitasoft license webservice.|acme|
|lic_ws_password|Password to Bonitasoft license webservice.|Secr3t|
|lic_sub_login|Subscription Login provided by Bonitasoft. It has an email address format.|john.doe@acme.com|
|lic_sub_password|Subscription Password provided by Bonitasoft.|SomePassword|
|lic_sub_id|Subscription ID provided by Bonitasoft. It's a string of 15 characters.|a0bA0000001B2Cd|
|lic_type|Type of license in [`production`\|`development`\|`qualification`\|`trial`].|trial|
|lic_name|Full name of the licensee. This field can only contain alphanumeric characters, dashes, underscores and spaces.|John Doe|
|lic_company|Company name.|ACME Inc|
|lic_email|Email address where the license will be sent.|john.doe@acme.com|


## Email notification variables

An email can be sent automatically with Bonita stack details at the end of the deployment phase. This feature uses [Ansible mail module](http://docs.ansible.com/ansible/latest/mail_module.html).

|Name|Mandatory|Default|Description|Sample|
|--- |--- |--- |--- |--- |
|mail_notification|N|false|Enable email notification or not.|false|
|mail_host|N|-|The SMTP server host.|smtp.gmail.com|
|mail_port|N|-|The SMTP port.|465|
|mail_username|N|-|The SMTP username.|john.doe<i>@</i>acme.com|
|mail_password|N|-|The SMTP password.|SomePassword|
|mail_from|N|-|The email-address the mail is sent from. May contain address and phrase.|john.doe<i>@</i>acme.com (John Doe)|
|mail_to|N|-|The email-address(es) the mail is being sent to. This is a comma-separated list, which may contain address and phrase portions.|John Doe &lt;john.doe<i>@</i>acme.com&gt;, Suzie Something &lt;sue<i>@</i>example.com&gt;|
