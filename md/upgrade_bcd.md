# Migrate from an earlier version of BCD

## Overview

This page explains how to upgrade your version of BCD.

Each of the versions follow the MAJOR.MINOR.PATCH format.

We have split this documentation in order to describe the differences between upgrades.

We will focus on :
- scenarios : indeed you may want to keep your existing scenarios. It will allow you to recreate the same kind of stack or manipulate an existing one (deploy, undeploy, destroy).
- Terraform metadata : "Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures." [https://www.terraform.io/docs/state/](https://www.terraform.io/docs/state/).
So if you want to be able to manage an existing stack (i.e. a stack already deployed from a previous version of BCD) you will need to copy paste this state.
- bcd controller
- Vagrant : this part applies only if you use Vagrant virtual machines.
- Living Application repositories : this part applies only for migrations from a 2.0.x

## How it works

### From a 1.0.x towards a ${varVersion}.y

Please follow the previous documentation describing the [upgrade from 1.0 toward 2.1](https://documentation.bonitasoft.com/bcd/2.1/upgrade_bcd). Then follow the instructions in the next section.

### From a 2.1.x towards a ${varVersion}.y

#### Scenarios

First, you will need to copy the files:
```bash
$ cp bonita-continuous-delivery_2.1.0/scenarios/*.yml bonita-continuous-delivery_${varVersion}.0/scenarios/
```
Then you will have to check `Breaking changes` section in the [release notes](release_notes.md). Indeed you will have to add the new variables `bcd_registry_user` and  `bcd_registry_password` in order to retrieve BCD dependencies from Bonita secured Docker registry.

Besides, the `bonita_edition` variable has been deprecated in BCD 3.0.0 (read [BCD 3.0.0 Release Notes](release_notes.md) for further details).  
Therefore you may remove this variable from your scenario files as it will be ignored.

#### Terraform

Copy the directory corresponding to your stack:
```bash
$ cp -r bonita-continuous-delivery_2.1.0/terraform/your_stack_name bonita-continuous-delivery_${varVersion}.0/terraform/
```

#### BCD controller

As is described in the "Installation guide" from the [Getting started](getting_started.md) you will need to load the last version of `bcd-controller_<version>.tar.zip Docker image.  
You can also directly use the secured Docker registry to retrieve the latest image.
```bash
$ docker login quay.io
Username: bonitasoft+john_doe_at_acme_com
Password:
$ docker pull quay.io/bonitasoft/bcd-controller
```

The username corresponds to `bcd_registry_user` and the password corresponds to `bcd_registry_password` described in the [Scenario file reference](scenarios.md). Both have been provided by your sales representative.

If you choosed to persist your docker console history ([see bcd controller](bcd_controller.md)) and want to keep it, then you should copy your `.bcd_bash_history` file from your old folder to your
new `bonita-continuous-delivery_${varVersion}.0/ folder`

```bash
$ cp bonita-continuous-delivery_2.1.0/.bcd_bash_history bonita-continuous-delivery_${varVersion}.0/.bcd_bash_history
```

#### Vagrant

As the controller access to them over the network it's not necessary to move them on the filesystem.

But if you plan to cleanup the old directory you can moved the vagrant data located inside `1-machine` or `2-machines` directories by copying the `.vagrant` subdirectories.
```bash
$ cp -r bonita-continuous-delivery_2.1.0/vagrant/1-machine/.vagrant bonita-continuous-delivery_${varVersion}.0/vagrant/1-machine/
$ cp -r bonita-continuous-delivery_2.1.0/vagrant/2-machines/.vagrant bonita-continuous-delivery_${varVersion}.0/vagrant/2-machines/
```

#### Living Application repositories

Move the directories where you have cloned your repositories.

For example :
```bash
$ cp -r bonita-continuous-delivery_2.1.0/bonita-vacation-management-example bonita-continuous-delivery_${varVersion}.0/bonita-vacation-management-example
```
