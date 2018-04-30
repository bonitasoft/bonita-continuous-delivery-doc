# Bonita Continuous Delivery Quick Start

The BCD add-on is provided as a `bonita-continuous-delivery_<version>.zip` archive.

This page describes how to use this archive to quickly start using Bonita Continuous Delivery solution.

## Before you begin

### Licensing prerequisites

To use BCD you must have an active Bonitasoft Subscription and a dedicated access to Bonitasoft license service. Indeed BCD requires such access in order to dynamically retrieve Bonita licenses while deploying your platform.

Contact your sales representative for more information.

### AWS Prerequisites

If you consider deploying to Amazon Web Services (AWS) platform, first read the [AWS prerequisites tutorial](aws_prerequisites.md) which describes how to configure AWS for BCD.

## Preparing the control workstation

### Step-by-step installation

In order to ease your environment setup, we provide a Docker image called **[BCD Controller](bcd_controller.md)** image which contains all required dependencies and tools for the control workstation (ie. the host from which Bonita Continuous Delivery commands are launched).

Follow these installation steps on your control workstation.

1.  Make sure Docker is installed as described in the [Install Docker](https://docs.docker.com/engine/installation/) documentation.

2.  Extract the `bonita-continuous-delivery_<version>.zip` archive:

        $ unzip bonita-continuous-delivery_<version>.zip

    This step creates a `bonita-continuous-delivery_<version>` directory which contains:

    *   Bonita Continuous Delivery Ansible playbooks and roles.
    *   A pre-built Docker image for the control workstation. This Docker image is provided as a `bcd-controller_<version>.tar.zip` archive located in the `bonita-continuous-delivery_<version>/docker` directory.
3.  Load the `bcd-controller_<version>.tar.zip` Docker image:

        $ cd bonita-continuous-delivery_<version>/docker
        $ unzip bcd-controller_<version>.tar.zip
        $ docker load -i bcd-controller_<version>.tar
        Loaded image: bonitasoft/bcd-controller:<version>
        Loaded image: bonitasoft/bcd-controller:latest

    The `bonitasoft/bcd-controller` Docker image is now available. To use the image, it is recommended to prepare the following local files which will be shared as Docker volumes in the next step:

    *   **`/local/path/to/bonita-continuous-delivery_<version>`** (mounted as `/home/bonita/bonita-continuous-delivery`) - The provided `bonita-continuous-delivery` directory contains Ansible playbooks for Bonita Continuous Delivery.
    *   <div class="list-group-item list-group-item-warning">This file is required for <strong>Provisioning</strong>. It is not required for Living App management.</div>
        **`ssh_private_key`** (mounted as `/home/bonita/.ssh/ssh_private_key`) - The `ssh_private_key` file is the SSH private key used to connect to your target machines. For AWS, this is the private part of your [EC2 key pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html). This file should only be accessible from your user (`chmod 600 ~/.ssh/ssh_private_key`).
    *   <div class="list-group-item list-group-item-warning">This file is required for <strong>Provisioning on AWS</strong>. It is not required for Living App management.</div>
        **`.boto`** (mounted as `/home/bonita/.boto`) - The [`.boto`](https://boto.readthedocs.io/en/latest/boto_config_tut.html) file contains AWS credentials used to make programmatic calls to AWS. The content of a `.boto` file is as follows:

            [Credentials]
            aws_access_key_id = <YOUR_AWS_ACCESS_KEY>
            aws_secret_access_key = <YOUR_AWS_SECRET_ACCESS_KEY>

4.  Make sure BCD dependencies are present.  
    BCD expects Bonita version-specific dependencies to be present in the `bonita-continuous-delivery_<version>/dependencies` directory. BCD dependencies are provided separately as a `bonita-continuous-delivery-dependencies_<bonita_version>.zip` archive.  
    Basically, the following artifacts must be extracted from the `bonita-continuous-delivery-dependencies_<bonita_version>.zip` archive into the `dependencies` directory:
    * `bonita-subscription_<bonita_version>.tar.gz`
    * `bonita-la-builder-<bonita_version>-exec.jar`
    * `bonita-sp-<bonita_version>-maven-repository.zip`
5.  Start a BCD Controller Docker container on the control workstation:

        $ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
            -v <local_path_to_.boto_file>:/home/bonita/.boto \
            -v <local_path_to_bonita-continuous-delivery_directory>:/home/bonita/bonita-continuous-delivery \
            -v <local_path_to_ssh_private_key_file>:/home/bonita/.ssh/<ssh_private_key> \
            bonitasoft/bcd-controller /bin/bash

### Complete installation example

Here is a complete example of how to install the BCD Controller Docker image.

**Warning**: This example uses _fake_ AWS credentials and SSH private key... :-)

Assuming you have a `bonita-continuous-delivery_2.0.0.zip` archive in your `$HOME` directory:

    $ cd $HOME
    $ unzip bonita-continuous-delivery_2.0.0.zip
    [...]

    $ cd bonita-continuous-delivery_2.0.0/docker

    $ unzip bcd-controller_2.0.0.tar.zip
    [...]
    $ docker load -i bcd-controller_2.0.0.tar
    Loaded image: bonitasoft/bcd-controller:2.0.0
    Loaded image: bonitasoft/bcd-controller:latest

Assuming you have a `bonita-continuous-delivery-dependencies_7.7.0.zip` archive in your `$HOME/bonita-continuous-delivery_2.0.0/dependencies` directory:

    $ cd $HOME/bonita-continuous-delivery_2.0.0/dependencies
    $ unzip bonita-continuous-delivery-dependencies_7.7.0.zip
    [...]

The next steps of this example are required when using BCD to provision a Bonita stack on AWS.

    $ cat ~/.boto
    [Credentials]
    aws_access_key_id = AKO9PHAI7YOSHAEMOHS6
    aws_secret_access_key = aicheeChe8Koh4aeSh3quum4tah8cohku3Hi8Eir

    $ ls -n ~/.ssh/bonita_us-west-2.pem
    -rw------- 1 1000 1000 1692 jul. 6 11:36 ~/.ssh/bonita_us-west-2.pem

Finally here is a sample command to start a BCD controller container:

    $ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
            -v ~/bonita-continuous-delivery_2.0.0:/home/bonita/bonita-continuous-delivery \
            -v ~/.boto:/home/bonita/.boto \
            -v ~/.ssh/bonita_us-west-2.pem:/home/bonita/.ssh/bonita_us-west-2.pem \
            bonitasoft/bcd-controller /bin/bash
    bonita@bcd-controller:~$
    bonita@bcd-controller:~$ cd bonita-continuous-delivery
    bonita@bcd-controller:~/bonita-continuous-delivery$

## Using Bonita Continuous Delivery add-on

::: warning
At this stage of the _Quick start_ guide, you should have a BCD controller container up and running.
:::

### Command Line Interface

From your BCD controller container, you can now run the `bcd` command to manage your Bonita stack and Living Application.

You can call `bcd` with the `--help` option to get available options and commands as follows:

```
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

An example scenario file named **uswest_performance.yml.EXAMPLE** is provided in the **scenarios** directory. Use this example to customize your scenario and save it with a `.yml` file extension.

::: info
Read the **[BCD Scenario reference](scenarios.md)** page for a comprehensive description of scenario variables.
:::
