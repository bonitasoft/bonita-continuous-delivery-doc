# Getting started

The BCD add-on is provided as a `bonita-continuous-delivery_<version>.zip` archive.

This page describes how to use this archive to start your journey with Bonita Continuous Delivery.


## Installation guide

In order to ease your environment setup, we provide a Docker image called **[BCD Controller image](bcd_controller.md)** which contains all required dependencies and tools for BCD.  
Then a **BCD Controller container** has to be started on the control host, ie. the host from which Bonita Continuous Delivery commands are launched.

![Bonita Continuous Delivery Controller](images/bcd_controller.png "Bonita Continuous Delivery Controller")

### Common installation steps

Follow these installation steps on your control host.

1.  Make sure Docker is installed as described in the [Install Docker](https://docs.docker.com/engine/installation/) documentation.

2.  Extract the `bonita-continuous-delivery_<version>.zip` archive:

        $ unzip bonita-continuous-delivery_<version>.zip

    This step creates a `bonita-continuous-delivery_<version>` directory which contains Bonita Continuous Delivery Ansible playbooks and roles.
    
::: warning
For windows users install in user folder (C:\Users\XXX) (If not, Docker won’t have the right to copy the volumes)
:::
    
3.  Pull the `quay.io/bonitasoft/bcd-controller:<version>` Docker image from secured registry.  
    The username corresponds to `bcd_registry_user` and the password corresponds to `bcd_registry_password` described in the [Scenario file reference](scenarios.md). Both have been provided by your sales representative.

        $ docker login quay.io
        Username: bonitasoft+john_doe_at_acme_com
        Password:
        Login Succeeded
        $ docker pull quay.io/bonitasoft/bcd-controller:<version>
        [...]
        Status: Downloaded newer image for quay.io/bonitasoft/bcd-controller:<version>
        $ docker logout quay.io

    The `quay.io/bonitasoft/bcd-controller:<version>` Docker image is now available on the control host.
4.  Make sure BCD dependencies are present.  
    BCD expects Bonita version-specific dependencies to be present in the `bonita-continuous-delivery_<version>/dependencies` directory. BCD dependencies are provided separately as a `quay.io/bonitasoft/bcd-dependencies:<bonita_version>` Docker data image.  
    Basically, the `/dependencies` volume provided by the `quay.io/bonitasoft/bcd-dependencies` Docker image must be mounted or copied into the `dependencies` directory. For instance, here is how to create a Docker named volume with BCD dependencies from the data image:

        $ docker login quay.io
        Username: bonitasoft+john_doe_at_acme_com
        Password:
        Login Succeeded
        $ docker run --rm -v bcd-dependencies-<bonita_version>:/dependencies quay.io/bonitasoft/bcd-dependencies:<bonita_version>
        $ docker logout quay.io

::: warning
:fa-exclamation-triangle: For linux user make sure your `user id` and `group id` is `1000` or refer to the `Running BCD controller with user ID different from 1000` paragraph in [BCD Controller image](bcd_controller.md)
:::

    The `bcd-dependencies-<bonita_version>` Docker named volume is now available and can be mounted with your BCD Controller.
5.  Start a BCD Controller Docker container on the control host:

        $ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
            -v <host_path_to_bonita-continuous-delivery_directory>:/home/bonita/bonita-continuous-delivery \
            -v bcd-dependencies-<bonita_version>:/home/bonita/bonita-continuous-delivery/dependencies/<bonita_version> \
            quay.io/bonitasoft/bcd-controller:<version> /bin/bash

    This command bind mounts the _bonita-continuous-delivery_ directory and the _bcd-dependencies-<bonita_version>_ volume on the control host into the BCD controller container.

::: info
You may also start a BCD Controller container using [Docker Compose](https://docs.docker.com/compose/) as described in the [BCD Controller image](bcd_controller.md) detailed documentation.
:::

### Additional steps for Provisioning

::: info
The following installation steps are required when using BCD for **Provisioning Bonita stacks**. They are not required for Living Application Management.
:::

#### Licensing prerequisites

While provisioning Bonita stacks, BCD automatically retrieves and installs Bonita licenses for the Bonita platform. Therefore, you must have an active Bonitasoft Subscription and a dedicated access to Bonitasoft license service in order to use BCD for Provisioning.

Contact your sales representative for more information.

#### AWS Prerequisites

If you consider deploying to Amazon Web Services (AWS) platform, first read the [AWS prerequisites tutorial](aws_prerequisites.md) which describes how to configure AWS for BCD.

BCD can create and delete [AWS EC2 instances](https://aws.amazon.com/ec2/) automatically by making programmatic calls to AWS. In order to use this feature, you must provide AWS credentials, for example through a [`.boto`](https://boto.readthedocs.io/en/latest/boto_config_tut.html) file. The content of a `.boto` file is as follows:

    [Credentials]
    aws_access_key_id = <YOUR_AWS_ACCESS_KEY>
    aws_secret_access_key = <YOUR_AWS_SECRET_ACCESS_KEY>

Then the `.boto` file has to be mounted from the control host into the BCD controller while starting the container. Add the following option to the `docker run` command:

    -v <host_path_to_.boto_file>:/home/bonita/.boto

#### Azure Prerequisites

If you consider deploying to Microsoft Azure cloud computing platform, first read the [Azure prerequisites tutorial](azure-prerequisites.md) which describes how to configure Azure for BCD.

#### SSH key file

BCD uses SSH to communicate with target machines. Therefore the BCD controller must have access to the related SSH private key file.  
For AWS, this is the private part of your [EC2 key pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) referenced in your AWS Console.  
With Azure, you will have to create an SSH key pair using [this tutorial on Linux or macOS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys) or [this tutorial on Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows).  
**:fa-exclamation-triangle: The SSH private key file must only be accessible from your user** (`chmod 600 <host_path_to_ssh_private_key_file>`).

Then the SSH private key file has to be mounted from the control host into the BCD controller while starting the container. Add the following option to the `docker run` command:

    -v <host_path_to_ssh_private_key_file>:/home/bonita/.ssh/<ssh_private_key>


## Complete installation example

Here is a complete example of how to install the BCD Controller Docker image.

**Warning**: This example uses _fake_ AWS credentials and SSH private key... :-)

Assuming you have a `bonita-continuous-delivery_3.4.1.zip` archive in your `$HOME` directory:

    $ cd $HOME
    $ unzip bonita-continuous-delivery_3.4.1.zip
    [...]

    $ docker login quay.io
    Username: bonitasoft+john_doe_at_acme_com
    Password:
    Login Succeeded
    $ docker pull quay.io/bonitasoft/bcd-controller:3.4.1
    [...]
    Status: Downloaded newer image for quay.io/bonitasoft/bcd-controller:3.04.1

    $ cd $HOME/bonita-continuous-delivery_3.4.1/dependencies

    $ docker run --rm -v bcd-dependencies-7.11.0:/dependencies quay.io/bonitasoft/bcd-dependencies:7.11.0
    [...]

    $ docker logout quay.io

The next steps of this example are required when using BCD to provision a Bonita stack on AWS.

    $ cat ~/.boto
    [Credentials]
    aws_access_key_id = AKO9PHAI7YOSHAEMOHS6
    aws_secret_access_key = aicheeChe8Koh4aeSh3quum4tah8cohku3Hi8Eir

    $ ls -n ~/.ssh/bonita_us-west-2.pem
    -rw------- 1 1000 1000 1692 jul. 6 11:36 ~/.ssh/bonita_us-west-2.pem

Finally here is a sample command to start a BCD controller container:

    $ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
            -v ~/bonita-continuous-delivery_3.4.1:/home/bonita/bonita-continuous-delivery \
            -v bcd-dependencies-7.11.0:/home/bonita/bonita-continuous-delivery/dependencies/7.11.0 \
            -v ~/.boto:/home/bonita/.boto \
            -v ~/.ssh/bonita_us-west-2.pem:/home/bonita/.ssh/bonita_us-west-2.pem \
            bonitasoft/bcd-controller /bin/bash
    bonita@bcd-controller:~$
    bonita@bcd-controller:~$ cd bonita-continuous-delivery
    bonita@bcd-controller:~/bonita-continuous-delivery$

## Using Bonita Continuous Delivery add-on

::: warning
At this stage of the _Getting started_ guide, you should have a BCD controller container up and running.
:::

### Command Line Interface

From your BCD controller container, you can now run the `bcd` command to manage your Bonita stack and Living Application.

You can call `bcd` with the `--help` option to get available options and commands as follows:

```bash
bonita@bcd-controller:~/bonita-continuous-delivery$ bcd --help
Usage: bcd [OPTIONS] COMMAND [ARGS]...

  Bonita Continuous Delivery CLI.

Options:
  -v, --verbose          Enable Ansible verbose mode
  -y, --yes              Execute action without confirmation prompt
  --nocolor              Turn output colorization off
  -s, --scenario PATH    YAML scenario file path - Optional if `BCD_SCENARIO`
                         environment variable is defined.
  -e, --extra-vars TEXT  Extra vars for Ansible (multiple) - Variables are
                         passed using the key=value syntax.
  -h, --help             Show this help message
  --version              Show the version and exit.

Commands:
  livingapp  Manage Bonita Living Application
  stack      Manage Bonita stack (infrastructure)
  version    Show BCD version information
```

::: info
Read the **[BCD Command-line interface](bcd_cli.md)** page for further information about the BCD CLI usage.
:::

### Scenarios

BCD requires a YAML configuration file called **Scenario** which describes your infrastructure and the Bonita stack to deploy.

Example scenario files are provided in the **scenarios** directory, including:
- **uswest_performance.yml.EXAMPLE** - a scenario for both stack and living application management with AWS provisioning
- **azure_northEurope.yml.EXAMPLE** - a scenario for both stack and living application management with Azure provisioning
- **build_and_deploy.yml.EXAMPLE** - a scenario for living application management only
- **manage_licenses.yml.EXAMPLE** - a scenario for license management only

Use these examples to customize your scenario and save it with a `.yml` file extension.

::: info
Read the **[BCD Scenario reference](scenarios.md)** page for a comprehensive description of scenario variables.
:::
