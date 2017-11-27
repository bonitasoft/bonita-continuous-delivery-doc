# Bonita Continuous Delivery Quick Start

Bonita Continuous Delivery (BCD) solution is provided as a `bonita-continuous-delivery_<version>.tar.gz` archive.

This page describes how to use this archive to quickly start using Bonita Continuous Delivery solution.


## Prepare your workstation

### Step-by-step installation

In order to ease your environment setup, we provide a [Docker](https://docs.docker.com/) image which contains all required dependencies and tools for the **Control Machine** (ie. the machine from which Bonita Continuous Delivery commands are launched).

Follow these installation steps on your workstation.

1. Make sure Docker is installed as described in the [Install Docker](https://docs.docker.com/engine/installation/) documentation.

1. Extract the `bonita-continuous-delivery_<version>.tar.gz` archive:
    ```
    $ tar xzf bonita-continuous-delivery_<version>.tar.gz
    ```
    This step creates a `ansible_<version>` directory which contains:
    - Bonita Continuous Delivery Ansible playbooks and roles.
    - A pre-built Docker image for the Control Machine. This Docker image is provided as a `bcd-controller_<version>.tar.gz` archive located in the `ansible_<version>/docker` directory.

1. Load the `bcd-controller_<version>.tar.gz` Docker image:
    ```
    $ cd ansible_<version>/docker
    $ gunzip -c bcd-controller_<version>.tar.gz | docker load
    Loaded image: bonitasoft/bcd-controller:<version>
    Loaded image: bonitasoft/bcd-controller:latest
    ```
    The `bonitasoft/bcd-controller` Docker image is now available. To use the image, it is recommended to prepare the following local files which will be shared as Docker volumes in the next step:
    - **`.boto`** (mounted as `/home/bonita/.boto`) - The [`.boto`](https://boto.readthedocs.io/en/latest/boto_config_tut.html) file contains AWS credentials used to make programmatic calls to AWS. The content of a `.boto` file is as follows:
        ```ini
        [Credentials]
        aws_access_key_id = <YOUR_AWS_ACCESS_KEY>
        aws_secret_access_key = <YOUR_AWS_SECRET_ACCESS_KEY>
        ```
    - **`ssh_private_key`** (mounted as `/home/bonita/.ssh/ssh_private_key`) - The `ssh_private_key` file is the SSH private key used to connect to your target machines. For AWS, this is the private part of your [EC2 key pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html). This file should only be accessible from your user (`chmod 600 ~/.ssh/ssh_private_key`).
    - **`/local/path/to/ansible_<version>`** (mounted as `/home/bonita/ansible`) - The provided `ansible` directory contains Ansible playbooks for Bonita Continuous Delivery.

1. Start a Docker container for the Control Machine:
    ```
    $ docker run --rm -ti --hostname control_machine --name bcd-controller \
        -v <local_path_to_.boto_file>:/home/bonita/.boto \
        -v <local_path_to_ansible_directory>:/home/bonita/ansible \
        -v <local_path_to_ssh_private_key_file>:/home/bonita/.ssh/<ssh_private_key> \
        bonitasoft/bcd-controller /bin/bash
    ```

### Complete installation example

Here is a complete example of how to install the Control Machine Docker image for Bonita Continuous Delivery.

**Warning**: This example uses *fake* AWS credentials and SSH private key... :-)

Assuming you have a `bonita-continuous-delivery_0.1.0.tar.gz` archive in your `$HOME` directory:

```
$ cd $HOME
$ tar xzf bonita-continuous-delivery_0.1.0.tar.gz

$ cd ansible_0.1.0/docker

$ gunzip -c bcd-controller_0.1.0.tar.gz | docker load
[...]
Loaded image: bonitasoft/bcd-controller:0.1.0
Loaded image: bonitasoft/bcd-controller:latest

$ cd $HOME

$ ls -nh
total 418M
drwxrwxr-x 12 1000 1000 4,0K juil.  6 11:34 ansible_0.1.0
-rw-rw-r--  1 1000 1000 418M juil.  6 11:34 bonita-continuous-delivery_0.1.0.tar.gz

$ cat ~/.boto
[Credentials]
aws_access_key_id = AKO9PHAI7YOSHAEMOHS6
aws_secret_access_key = aicheeChe8Koh4aeSh3quum4tah8cohku3Hi8Eir

$ ls -n ~/.ssh/bonita_us-west-2.pem
-rw------- 1 1000 1000 1692 jul. 6 11:36 ~/.ssh/bonita_us-west-2.pem

$ docker run --rm -ti --hostname control_machine --name bcd-controller \
        -v ~/.boto:/home/bonita/.boto \
        -v ~/ansible_0.1.0:/home/bonita/ansible \
        -v ~/.ssh/bonita_us-west-2.pem:/home/bonita/.ssh/bonita_us-west-2.pem \
        bonitasoft/bcd-controller /bin/bash
bonita@control_machine:~$
bonita@control_machine:~$ cd ansible
bonita@control_machine:~/ansible$
```


## Use Bonita Continuous Delivery solution

We provide a [BCD Command Line Interface](cli.md) (CLI) to execute Bonita Continuous Delivery commands.

The BCD CLI is directly available as a `bcd` command when using the `bonitasoft/bcd-controller` Docker image.

The `bcd` command must be launched from the `ansible` directory where Bonita Continuous Delivery Ansible playbooks are located.

You can call the `bcd` command with the `--help` option to get available options and commands:
```
bonita@control_machine:~$
bonita@control_machine:~$ cd ansible
bonita@control_machine:~/ansible$ bcd --help
Usage: bcd [OPTIONS] COMMAND1 [ARGS]... [COMMAND2 [ARGS]...]...

  Bonita Continuous Delivery CLI.

Options:
  -s, --scenario PATH    YAML scenario file (required)
  -y, --yes              Execute action without confirmation prompt
  -v, --verbose          Enable Ansible verbose mode
  -e, --extra-vars TEXT  Extra vars for Ansible (multiple) - Variables are
                         passed using the key=value syntax.
  -h, --help             Show this help message

Commands:
  status     Show the platform status
  create     Create AWS instances
  deploy     Deploy Bonita stack
  undeploy   Undeploy Bonita stack
  destroy    Destroy AWS machines (EC2 instances)
```
