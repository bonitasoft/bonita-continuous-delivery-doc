# Bonita Platform Provisioning scenarios

With scenarios, you can define your infrastructure, Bonita installation and potential tests you want to execute. A scenario is a YAML Ansible variable file injected in Ansible with the command ```-e "@/path/to/scenario.yml"```. The variables described in a scenario have precedence over those defined in the playbooks or the group vars. It is possible to overload these variables with a declaration of additional variables after this, in order to test multiple alternatives (for instance overloading ```bonita_version``` can be interesting if one wants to test multiple versions of Bonita.

The format is flat as shows this sample :
```yaml
bcd_stack_id: vagrant
configuration: vagrant_vars
bonita_cluster_mode: false
bonita_cluster_qty: 1
```
The following sections describe the main and mandatory variables.

## Infrastructure and misc.
Some parameters are mandatory for general provisioning and test description.

|Name |Mandatory|Default|Description|Sample|
|-|-|-|-|-|
|bcd_stack_id|Y| |Bonita stack identifier used to group machines by stack membership. For AWS deployments the characters allowed are limited to letters, numbers and underscore.|uswest2_test|
|configuration|Y | |Configuration filename. Initialize main variables. Choose between aws_vars, vagrant_vars|aws_vars|
|ansible_ssh_user|N|ubuntu|SSH user|ubuntu|
|ansible_private_key_file|Y |-|Path for the private SSH key.|~/.ssh/jenkins_ansible_us-west-2.pem|

### Vagrant scenarios
In case of Vagrant scenario, for testing purposes. The configuration file value should be ```vagrant_vars```.

### On-premises scenarios
In case of on-premises scenario. An inventory with all the corresponding machines should be used. A configuration file similar to ```vagrant_vars``` can be used.

### AWS Cloud scenarios
In case of AWS deployments. The configuration file value should be  ```aws_vars``` in this case.

|Name |Mandatory|Default|Description|Sample|
|-|-|-|-|-|
|ec2_region|Y| |AWS region. |us-west-2|
|ec2_security_group|Y| |Security group id for the target instances|sg-944888f1|
|ec2_security_group_name|Y | |Security group name for the target instances |default|
|ec2_subnet_ids|Y | |Subnet id for the target instances |subnet-aa14e0cf|
|ec2_keypair|Y | |Key pair name for the target instances. Linked to ansible_private_key_file. |jenkins_ansible_us-west-2|
|ec2_discovery_iam_role|N| |Name of the IAM role to attach to EC2 instances to allow Hazelcast EC2 auto-discovery. This is MANDATORY when deploying a Bonita cluster on AWS with BCD.|ClusterBCD|
|boto_path|N|~/.boto|Path to the Boto file with the AWS credentials.||
|rds|N|false|Precises if we use RDS for the Bonita BPM databases or if we use a Docker container deployed in an EC2 instance instead (default)|true|

The following figure describes for each tier the important variables.

|Name |Mandatory|Default|Description|Sample|
|-|-|-|-|-|
|aws_ami|Y | |Common AMI for all EC2 instances. Depends on region. Should be Ubuntu 14.04 (trusty) and ebs-ssd type. |ami-fc4f5e85|
|aws_database_instance_type|Y | |Instance type for the database instance |t2.micro|
|aws_bonita_instance_type|Y | |Instance type for the Bonita instance |t2.micro|
|aws_load_balancer_instance_type|Y | |Instance type for the load balancer instance |t2.micro|

## Bonita
Some parameters should be precised for Bonita. The corresponding Docker tar files should be present in ```docker``` directory.

|Name |Mandatory|Default|Description|Sample|
|-|-|-|-|-|
|bonita_version|Y||Bonita version. Used for the Docker tar.gz filename|7.5.0|
|bonita_edition|N|yes|performance edition of Bonita by default, other values: teamwork, efficiency, community. Docker tar filename depends on this. |performance|
|bonita_cluster_mode|N|false|Activates cluster mode for Bonita |false|
|bonita_cluster_qty|N|1|Number of instances for Bonita. This variable is also used in EC2 mode for the number of Bonita EC2 instance.| 1|
|bonita_port|N|8081|Bonita HTTP port |8081|
|bonita_db_vendor|N|postgres|Database vendor (postgres, mysql, oracle) |postgres|
|bonita_db_host|N||Database host (usually retrieved from inventory) |postgres.a1bac2defghi.us-west-2.rds.amazonaws.com|
|bonita_db_port|N|5432|Database port |5432|
|bonita_db_admin_user|N|postgres|Admin user on database instance|root|
|bonita_db_admin_pass|N|mysecretpassword|Admin password on database instance|password|
|bonita_http_api|N|false|Activates the Bonita [HTTP API](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc9)|true|
|bonita_rest_api_dyn_auth_checks|N|true|Activates [dynamic authorization checking](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc2) on REST API|false|

## Email notification
An email can be sent automatically with Bonita stack details at the end of the deployment phase. This feature uses [Ansible mail module](http://docs.ansible.com/ansible/latest/mail_module.html).

|Name |Mandatory| Default|Description|Sample|
|-|-|-|-|-|
|mail_notification|N|false|Enable email notification or not.|false|
|mail_host|N|-|The SMTP server host.|smtp.gmail.com|
|mail_port|N|-|The SMTP port.|465|
|mail_username|N|-|The SMTP username.|john.doe<i>@</i>acme.com|
|mail_password|N|-|The SMTP password.|SomePassword|
|mail_from|N|-|The email-address the mail is sent from. May contain address and phrase.|john.doe<i>@</i>acme.com (John Doe)|
|mail_to|N|-|The email-address(es) the mail is being sent to. This is a comma-separated list, which may contain address and phrase portions.|John Doe &lt;john.doe<i>@</i>acme.com&gt;, Suzie Something &lt;sue<i>@</i>example.com&gt;|

## Keeping sensitive data

If you want to keep your sensitive data such as passwords or keys in an encrypted way rather than as plain text. 
You can create a separate YAML file with all your sensitive data.

We propose these parameters but you can add others that you consider sensitive.
|Name |Mandatory|
|-|-|
|mail_username|N|
|mail_password|N|
|bonita_db_user|N|
|bonita_db_pass|N|
|bonita_biz_db_user|N|
|bonita_biz_db_pass|N|
|bonita_platform_login|N|
|bonita_platform_password|N|
|bonita_tenant_login|N|
|bonita_tenant_password|N|
|bonita_db_admin_user|N|
|bonita_db_admin_pass|N|
|lic_ws_login|Y|
|lic_ws_password|Y|
|lic_sub_login|Y|
|lic_sub_password|Y|
|lic_sub_id|Y|

To encrypt this file, you can you the command line tool `ansible-vaul` as shown in the documentation of 
[Ansible Vaul](http://docs.ansible.com/ansible/latest/vault.html).

To enable this feature with `bcd` you can send this encrypted file through the option `-e ` with `@` syntax:

```bash
-e "@/path/to/encrypted_file.yml"
```
This option will prompt a password to decrypt the encrypted file sent it.


If you want to automate the decryption process, you can create a file with the password and naming it with the prefix 
`pass_` .
To use this password file with `bcd`, you have to send this file through the option `-e ` with `@` syntax:
```bash
-e "@/path/to/pass_mydev"
```

### Examples
#### Prompting password
```
$ bcd -s scenarios/uswest2_cluster.yml -e "@encrypte_file" create
Vault password:

```

#### Sending password file
```
$ bcd -s scenarios/uswest2_cluster.yml -e "@encrypte_file" -e "@pass_mydevpass" -y create
``` 