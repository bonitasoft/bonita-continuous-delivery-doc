# Migrate from an earlier version of BCD

## Overview

This page explains how to upgrade your version of BCD.

Each versions follow the MAJOR.MINOR.PATCH format.

On each steps we will specify if there are differences for a major upgrade (1.0.x to 2.0.y) or a patch (2.0.x to 2.0.y).

We will focus on :
- scenarios
- Terraform metadata
- bcd controller
- Vagrant
- Living Application repositories

## How it works

### Scenarios

First you may want to keep your existing scenarios. It will allow you to recreate the same kind of stack or manipulate an existing one (deploy, undeploy, destroy).

If you migrate from a 1.0.x towards a 2.0.y you will first need to copy the files
```
cp bonita-continuous-delivery_1.0.5/scenarios/*.yml bonita-continuous-delivery_2.0.0/scenarios/
```
Then you will have to check `Breaking changes` section into the [release notes](release_notes.md). Indeed you may want to override `bonita_http_api` default value that have changed. Or for deployments that do not use `aws` for `bcd_provider` you will need to replace `on-premises` by `static_inventory`.

If you migrate from a 2.0.x towards a 2.0.y you will only need to copy the files.

```
cp bonita-continuous-delivery_2.0.0/scenarios/*.yml bonita-continuous-delivery_2.0.2/scenarios/
```

### Terraform

"Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures." [https://www.terraform.io/docs/state/](https://www.terraform.io/docs/state/).

So if you want to be able to manage an existing stack (ie a stack already deployed from a previous version of BCD) you will need to copy paste this state.

If you migrate from a 1.0.x version to a 2.0.y you will need first to create a directory named like your stack inside Terraform directory and then copy into it the corresponding tfstate file.
```
mkdir bonita-continuous-delivery_2.0.0/terraform/your_stack_name
cp bonita-continuous-delivery_1.0.5/terraform/your_stack_name.tfstate bonita-continuous-delivery_2.0.0/terraform/your_stack_name/your_stack_name.tfstate
```
If you migrate from a 2.0.x towards a 2.0.y you will only need to copy the directory corresponding to your stack.
```
cp -r bonita-continuous-delivery_2.0.0/terraform/your_stack_name bonita-continuous-delivery_2.0.2/terraform/
```

### BCD controller

Like described into the "Installation guide" from the [Getting started](getting_started.md) you will need to load the last version of `bcd-controller_<version>.tar.zip Docker image.

### Vagrant

Note : if you use Vagrant, your VM are stored under the folder named `vagrant`.

If you migrate from a 1.0.x towards a 2.0.y, the operating system of Vagrant VMs has been changed from Ubuntu Trusty to Xenial.

As the controller access to them over the network it's not necessary to move them on the filesystem.

But if you migrate from a 2.0.x towards a 2.0.y and plan to cleanup the old directory you can first moved the vagrant data located inside `1-machine` or `2-machines` directories by copying the `.vagrant` subdirectories.
```
cp -r bonita-continuous-delivery_2.0.0/vagrant/1-machine/.vagrant bonita-continuous-delivery_2.0.2/vagrant/1-machine/
cp -r bonita-continuous-delivery_2.0.0/vagrant/2-machines/.vagrant bonita-continuous-delivery_2.0.2/vagrant/2-machines/
```

### Living Application repositories

This part concerns only migration from a 2.0.x towards a 2.0.y.

In this case you just have to move the directories where you have cloned your repositories.

For example :
```
cp -r bonita-continuous-delivery_2.0.0/bonita-vacation-management-example bonita-continuous-delivery_2.0.2/bonita-vacation-management-example
```
