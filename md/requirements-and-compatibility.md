# Requirements and compatibility

This page describes which system requirements need to be met in order to use the Bonita Continuous Delivery Add-On.  
You will also find information about BCD and Bonita version compatibility.


## System requirements

There are different environments where requirements are to be met for BCD:
- **Control host** - the host where your [BCD controller](bcd_controller.md) will run.
- **Bonita stack hosts** - the target hosts where your Bonita stack will be deployed using BCD.
- **Jenkins server** - the server where BCD commands are executed by Jenkins Continuous Integration platform.


### Control host

The **Docker Engine** must be up and running on your control host prior to using BCD. BCD is compatible with Docker version 17.04.0 and above.

You may have [Docker Community Edition (Docker CE)](https://docs.docker.com/install/) or [Docker Enterprise Edition (Docker EE)](https://docs.docker.com/ee/supported-platforms/) installed on your host to start using BCD.

::: info
The Docker Engine may be installed on Linux, MacOS, Microsoft Windows or Cloud platforms as described in [Docker installation guide](https://docs.docker.com/install/).
:::


### Bonita stack hosts

::: warning
These requirements must be fulfilled when using BCD for **Provisioning**. They apply to target hosts where the Bonita stack will be deployed.
:::

Bonita stack hosts may be running **on-premises** or in **cloud platforms** like [Amazon Web Services (AWS)](https://aws.amazon.com/), [Microsoft Azure](https://azure.microsoft.com/), [Google Cloud](https://cloud.google.com/) or [OpenStack](https://www.openstack.org/).  
* with the AWS platform, BCD enables you to create and delete EC2 instances of the Bonita stacks automatically. To do so, read the [AWS configuration for BCD tutorial](aws_prerequisites.md).
* with the Azure platform, BCD enables you to create and delete Azure resources of the Bonita stacks automatically. To do so, read the [Azure configuration for BCD tutorial](azure-prerequisites.md).
* with other platforms, target hosts must be up and running prior to using BCD. Basic setup for target hosts is explained below.

The system requirements for the target hosts are as follows:
- Minimum hardware requirements: 1 CPU / 1GiB RAM / 8GiB storage (Equivalent to AWS EC2 `t2.micro` instance)
- Supported operating systems: Ubuntu-14.04, Ubuntu-16.04

Besides, the following technical requirements must be met:
- Target hosts must have **Internet access** in order to communicate with Bonitasoft License webservice.  
  It is also required to install packages and to pull Docker public images for the database instance when applicable.
- Target hosts must be accessible from the BCD controller using **SSH public-key authentication**.
- The user configured to connect to the target hosts must have `sudo` or `root` privileges in order to install the Bonita stack.

<div class="well">

#### Basic setup for target hosts

Here are the steps to manually install a target host for BCD:
1. Install one of the supported operating systems.
2. Create a user and grant `sudo` or `root` privileges.
3. Generate SSH keys for this user.

The SSH private key has to be retrieved on your BCD controller and its path configured in your BCD scenario.

</div>


### Jenkins server

BCD commands may be executed by a Jenkins agent (slave) which runs the BCD controller Docker container.  
Therefore the Jenkins server shall meet the same requirements as the Control host.



## Version compatibility

The following table shows which BCD versions support specific Bonita releases.  
This compatibility information is also specified in the [Release notes](release_notes.md) for each version of BCD.

<div style="min-width: 270px; max-width: 370px;">

| BCD version | Bonita release |
|-------------|----------------|
| 2.0.x       | 7.7.x          |
| 3.0.x       | < 7.7.x        |

</div>

::: warning
If a Bonita release is not listed in the previous table, then it is not supported by the BCD Add-On.
:::
