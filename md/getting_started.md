# Getting started

The BCD add-on is provided as a `bonita-continuous-delivery_<version>.zip` archive.

This page describes how to use this archive to start your journey with Bonita Continuous Delivery.


## Installation guide

In order to ease your environment setup, we provide a Docker image called **[BCD Controller](bcd_controller.md)** image which contains all required dependencies and tools for the control workstation (ie. the host from which Bonita Continuous Delivery commands are launched).

### Common installation steps

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

    The `bonitasoft/bcd-controller` Docker image is now available on the control workstation.
4.  Make sure BCD dependencies are present.  
    BCD expects Bonita version-specific dependencies to be present in the `bonita-continuous-delivery_<version>/dependencies` directory. BCD dependencies are provided separately as a `bonita-continuous-delivery-dependencies_<bonita_version>.zip` archive.  
    Basically, the following artifacts must be extracted from the `bonita-continuous-delivery-dependencies_<bonita_version>.zip` archive into the `dependencies` directory:
    * `bonita-subscription_<bonita_version>.tar.gz`
    * `bonita-la-builder-<bonita_version>-exec.jar`
    * `bonita-sp-<bonita_version>-maven-repository.zip`
5.  Start a BCD Controller Docker container on the control workstation:

        $ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
            -v <host_path_to_bonita-continuous-delivery_directory>:/home/bonita/bonita-continuous-delivery \
            bonitasoft/bcd-controller /bin/bash

    This command bind mounts the _bonita-continuous-delivery_ directory on the control workstation into the BCD controller container.


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

Then the `.boto` file has to be mounted from the control workstation into the BCD controller while starting the container. Add the following option to the `docker run` command:

    -v <host_path_to_.boto_file>:/home/bonita/.boto

#### SSH key file

BCD uses SSH to communicate with target machines. Therefore the BCD controller must have access to the related SSH private key file. For AWS, this is the private part of your [EC2 key pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html). **This file must only be accessible from your user** (`chmod 600 <host_path_to_ssh_private_key_file>`).

Then the SSH key file has to be mounted from the control workstation into the BCD controller while starting the container. Add the following option to the `docker run` command:

    -v <host_path_to_ssh_private_key_file>:/home/bonita/.ssh/<ssh_private_key>


## Complete installation example

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
At this stage of the _Getting started_ guide, you should have a BCD controller container up and running.
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
