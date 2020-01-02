# Migrate from an earlier version of BCD

## Overview

This page explains how to upgrade your version of BCD.

Each of the versions follow the MAJOR.MINOR.PATCH format.

We have split this documentation in order to describe the differences for a major upgrade (1.0.x to 2.1.y) or a minor (2.0.x to 2.1.y).

We will focus on :
- scenarios : indeed you may want to keep your existing scenarios. It will allow you to recreate the same kind of stack or manipulate an existing one (deploy, undeploy, destroy).
- Terraform metadata : "Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures." [https://www.terraform.io/docs/state/](https://www.terraform.io/docs/state/).
So if you want to be able to manage an existing stack (i.e. a stack already deployed from a previous version of BCD) you will need to copy paste this state.
- bcd controller
- Vagrant : this part applies only if you use Vagrant virtual machines.
- Living Application repositories : this part applies only for migrations from a 2.0.x

## How it works

### From a 1.0.x towards a 2.1.y

#### Scenarios

You will first need to copy the files
```
cp bonita-continuous-delivery_1.0.5/scenarios/*.yml bonita-continuous-delivery_2.1.0/scenarios/
```
Then you will have to check `Breaking changes` section into the [release notes](release_notes.md). Indeed you may want to override `bonita_http_api` default value that has changed. Or for deployments that do not use `aws` for `bcd_provider` you will need to replace `on-premises` by `static_inventory`.

#### Terraform


You will need first to create a directory named like your stack inside Terraform directory and then copy into it the corresponding tfstate file.
```
mkdir bonita-continuous-delivery_2.1.0/terraform/your_stack_name
cp bonita-continuous-delivery_1.0.5/terraform/your_stack_name.tfstate bonita-continuous-delivery_2.1.0/terraform/your_stack_name/your_stack_name.tfstate
```

#### BCD controller

As is described into the "Installation guide" from the [Getting started](getting_started.md) you will need to load the last version of `bcd-controller_<version>.tar.zip` Docker image.

#### Vagrant

The operating system of Vagrant VMs has been changed from Ubuntu Trusty to Xenial.

As the controller access to them over the network it's not necessary to move them on the filesystem.

### From a 2.0.x towards a 2.1.y

#### Scenarios

You just need to copy the files.

```
cp bonita-continuous-delivery_2.0.0/scenarios/*.yml bonita-continuous-delivery_2.1.0/scenarios/
```

#### Terraform

Copy the directory corresponding to your stack.
```
cp -r bonita-continuous-delivery_2.0.0/terraform/your_stack_name bonita-continuous-delivery_2.1.0/terraform/
```

#### BCD controller

As is described into the "Installation guide" from the [Getting started](getting_started.md) you will need to load the last version of `bcd-controller_<version>.tar.zip` Docker image.

If you choosed to persist your docker console history ([see bcd controller](bcd_controller.md)) and want to keep it, then you should copy your `.bcd_bash_history` file from your old folder to your
new `bonita-continuous-delivery_${varVersion}.0/ folder`

```bash
$ cp bonita-continuous-delivery_2.0.0/.bcd_bash_history bonita-continuous-delivery_2.0.2/.bcd_bash_history
```

#### Vagrant

As the controller access to them over the network it's not necessary to move them on the filesystem.

But if you plan to cleanup the old directory you can moved the vagrant data located inside `1-machine` or `2-machines` directories by copying the `.vagrant` subdirectories.
```
cp -r bonita-continuous-delivery_2.0.0/vagrant/1-machine/.vagrant bonita-continuous-delivery_2.1.0/vagrant/1-machine/
cp -r bonita-continuous-delivery_2.0.0/vagrant/2-machines/.vagrant bonita-continuous-delivery_2.1.0/vagrant/2-machines/
```

#### Living Application repositories

Move the directories where you have cloned your repositories.

For example :
```
cp -r bonita-continuous-delivery_2.0.0/bonita-vacation-management-example bonita-continuous-delivery_2.1.0/bonita-vacation-management-example
```
