# BCD controller Docker image

The BCD controller image provides a ready-to-use environment to launch Bonita Continuous Delivery CLI and Ansible playbooks.


## Installation

A pre-built BCD controller image is provided with BCD deliverable as a `bcd-controller_<version>.tar.zip` archive. It is located in the `bonita-continuous-delivery_<version>/docker` directory.

Load this image into Docker engine as follows:
```
$ cd bonita-continuous-delivery_<version>/docker
$ unzip bcd-controller_<version>.tar.zip
$ docker load -i bcd-controller_<version>.tar
Loaded image: bonitasoft/bcd-controller:<version>
Loaded image: bonitasoft/bcd-controller:latest
```


## Usage

To enter the BCD controller environment, a Docker container has to be started on your control workstation using the BCD controller image.

Besides the following files have to be bind mounted from the control workstation to make them available to the BCD controller container:
- **`/host/path/to/bonita-continuous-delivery_<version>`** (mounted as `/home/bonita/bonita-continuous-delivery`) - This directory provides BCD Ansible playbooks and is known as the `BCD_HOME` directory.
- <div class="list-group-item list-group-item-warning">This file is required for <strong>Provisioning</strong>. It is not required for Living App management.</div>
  
  **`ssh_private_key`** (mounted as `/home/bonita/.ssh/ssh_private_key`) - The `ssh_private_key` file is the SSH private key used to connect to your target machines. For AWS, this is the private part of your [EC2 key pair](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html). This file should only be accessible from your user (`chmod 600 ~/.ssh/ssh_private_key`).
- <div class="list-group-item list-group-item-warning">AWS credentials are required for <strong>Provisioning on AWS</strong>. It is not required for Living App management.</div>
  
  **`.boto`** (mounted as `/home/bonita/.boto`) - The [`.boto`](https://boto.readthedocs.io/en/latest/boto_config_tut.html) file contains AWS credentials used to make programmatic calls to AWS. Indeed dynamic Amazon EC2 inventory management for Ansible runs on top of [Boto](https://aws.amazon.com/sdk-for-python/). The content of a `.boto` file is as follows:
    ```ini
    [Credentials]
    aws_access_key_id = <YOUR_AWS_ACCESS_KEY>
    aws_secret_access_key = <YOUR_AWS_SECRET_ACCESS_KEY>
    ```
::: info
However AWS credentials can be provided in other ways, for instance with [AWS single sign-on](aws_sso.md).
:::

Once the required files are prepared, the BCD controller container can be started in different ways described hereafter.


### Starting a BCD controller with `docker run`

The first method to start a BCD Controller Docker container on the control workstation is to use the `docker run` command.

**Example**:

```
$ docker run --rm -t -i --name bcd-controller \
    -v <host_path_to_.boto>:/home/bonita/.boto \
    -v <host_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <host_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    bonitasoft/bcd-controller /bin/bash
```


### Starting a BCD controller with `docker-compose run`

The BCD controller container can also be created and started using [Docker Compose](https://docs.docker.com/compose/). This method enables to configure bind mount volumes in a `docker-compose.yml` file instead of through command-line options.

**Example:**

Here is a `docker-compose.yml` file example that you can create in your `BCD_HOME` directory.

```yaml
version: "3.2"

services:

  bcd:
    image: bonitasoft/bcd-controller
    hostname: bcd-controller
    stdin_open: true
    tty: true
    command: /bin/bash
    working_dir: /home/bonita/bonita-continuous-delivery
    volumes:
      - .:/home/bonita/bonita-continuous-delivery
      - <host_path_to_.boto>:/home/bonita/.boto
      - <host_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key>
```

Then start the BCD controller container interactively with:
```
$ docker-compose run --rm bcd
```
