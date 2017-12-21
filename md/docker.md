# BCD controller Docker image

This Docker image provides a ready-to-use environment to launch Bonita Continuous Delivery Ansible playbooks.

## Install the image

### From pre-built image

A pre-built BCD controller image is provided with BCD deliverable as a `bcd-controller_<version>.tar.gz` archive. It is located in the `bonita-continuous-delivery_<version>/docker` directory.

Load this image into Docker engine as follows:
```
$ cd bonita-continuous-delivery_<version>/docker
$ gunzip -c bcd-controller_<version>.tar.gz | docker load
Loaded image: bonitasoft/bcd-controller:<version>
Loaded image: bonitasoft/bcd-controller:latest
```

### From source

You can also build the image from source. Its Dockerfile is provided with BCD deliverable in the `bonita-continuous-delivery_<version>/docker` directory.

Build the image as follows:
```
$ cd bonita-continuous-delivery_<version>/docker
$ docker build -t bonitasoft/bcd-controller .
```

## Use the image

### Configure AWS credentials

Dynamic Amazon EC2 inventory management for Ansible runs on top of [Boto](https://aws.amazon.com/sdk-for-python/).

Boto's configuration requires the following `.boto` file to exist in the Docker container's `/home/bonita` directory.

```
[Credentials]
aws_access_key_id = <YOUR_AWS_ACCESS_KEY>
aws_secret_access_key = <YOUR_AWS_SECRET_ACCESS_KEY>
```

### Start a container

```
$ docker run --rm -t -i --name bcd-controller \
    -v <local_path_to_.boto>:/home/bonita/.boto \
    -v <local_path_to_bonita-continuous-delivery_folder>:/home/bonita/bonita-continuous-delivery \
    -v <local_path_to_ssh_private_key>:/home/bonita/.ssh/<ssh_private_key> \
    bonitasoft/bcd-controller /bin/bash
$ cd /home/bonita/bonita-continuous-delivery
```
