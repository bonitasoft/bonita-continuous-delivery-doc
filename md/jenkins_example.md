# BCD with Jenkins

The BCD controller can not only be run interactively, but also within standard Continuous Delivery servers such as [Jenkins automation server](https://jenkins.io/).

The `bonita-continuous-delivery_<version>.tar.gz` archive provides a `jenkins-example` directory which contains a minimal working example of a Continuous Delivery platform with Jenkins and BCD.

This example will start a pre-configured Jenkins server with a BCD-aware Jenkins slave and ready-to-use Jenkins jobs.  
It is provided as a [Docker Compose](https://docs.docker.com/compose/) project.

Therefore you should have **Docker and Docker Compose** installed on the target host to run this example.

This example is provided with Compose file version 3.2 which requires Docker Compose 1.12.0+ and Docker Engine 17.04.0+. Refer to [Compose and Docker compatibility matrix](https://docs.docker.com/compose/compose-file/#compose-and-docker-compatibility-matrix) and [Compose release notes](https://docs.docker.com/release-notes/docker-compose/) for further details.

::: info
In this example, a Jenkins slave is created from a Docker image which extends the BCD Controller image. Then an example job is defined as a [Jenkins Pipeline](https://jenkins.io/doc/book/pipeline/) job in which `bcd` commands are invoked.  
This allows you to easily re-use parts of this example in your own Jenkins instance.
:::

## Step-by-step run guide

Here is a step-by-step guide to run this example.  
The following commands are to be executed on the target host where Jenkins is to be installed.

1.  Configure the Docker Compose project.
    *   Rename the `docker-compose.override.yml.EXAMPLE` file to `docker-compose.override.yml` (remove the `.EXAMPLE` extension).
    *   Configure `environment` and `volumes` sections and save.
2.  Configure initial secrets used by Jenkins and BCD.
    *   Rename the `secrets/*Password.EXAMPLE` files to `secrets/*Password` (remove the `.EXAMPLE` extension).  
        With most Linux distributions, you can do this with the `rename` command line tool as follows: `rename 's/.EXAMPLE//' *.EXAMPLE`).
    *   Configure each `*Password` file with appropriate credentials.
3.  Make sure the BCD Controller Docker image is loaded as described in the installation section of the [BCD controller Docker image](bcd_controller.md) page.
4.  Create and start containers with Docker Compose.
    ```
    $ cd jenkins_example
    $ docker-compose up -d
    ```

As a result Jenkins is up and running on port `8080` of the target host.  
You can now log-in to Jenkins using one of the pre-configured users:

*   `admin` - User with administration rights. Password has been initialized with `secrets/adminPassword`.
*   `bonita` - User with limited rights. Password has been initialized with `secrets/bonitaPassword`.

::: warning
**Important Note**: Prepare your BCD scenario and dependencies as usual before using the pre-configured Jenkins jobs.
:::

## Pre-configured Jenkins jobs

Once authenticated to Jenkins you can now launch pre-configured jobs.  
This example project provides the following jobs:

*   `bcd-pipeline` - this job shows how the BCD CLI can be used to build a Continuous Delivery pipeline for a Bonita Living Application.
*   `bcd-destroy` - this job allows to destroy AWS EC2 machines when using BCD with AWS cloud provider.
