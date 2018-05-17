# How to deploy Bonita on existing server running

BCD can not only deploy your Bonita Stack in the cloud. You can also deploy your stack on your existing hosts running.
On this page, you will find detailed information about the usage of BCD with your existing hosts and finally, 
you will see an example, creating virtual machines with Vagrant and deploying your Bonita application with BCD.

## Preparing configuration files
There is two main files to configure:
### Inventory
This file defines the hosts and groups of hosts upon which BCD will operates. The location of the inventory files is
`bonita-continuous-delivery_x.x.x/ansible/inventory/`.
and `static-inventory-1-machine.EXAMPLE` in the same directory.
In this file, there are two group names, `database` and `bonita` that must appear and under each one you have to describe
your machines, and a group with the same name of your bonita stack id grouping the `database` and `bonita` groups.
Also, you have to specify a group variables for `bonita` group with:
* `bonita_address`, that is the IP of Bonita host
* `bonita_db_host`, that is the IP of your database

You can see an example in `bonita-continuous-delivery_x.x.x/ansible/inventory/` for one or two machines.  

::: info
It is recommended to have a clear understanding on [Ansible Inventories](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)
:::                                                  

### Scenario
The scenarios follows the same rules as described in the [Scenario file reference](scenarios.md). There are five
parameters that you don't have to forget to set-up: 
* bcd_provider: Infrastructure provider. It has to be set with `static-inventory`
* ansible_ssh_user: The ssh user to use.
* ansible_ssh_private_key_file: Private key file used by ssh.
* bcd_inventory: Ansible inventory path required when `bcd_provider` is `static-inventory`
* configuration: Configuration filename used to group variables for common deployment types.

You can see an example of scenario in `bonita-continuous-delivery_x.x.x/scenarios/static_inventory.yml.EXAMPLE`

## Deploy with Vagrant
You will see a complete example of deploying on existing virtual machines created with Vagrant.

::: warning
For this tutorial it is highly recommended to have a clear understanding of [Vagrant](https://www.vagrantup.com/intro/index.html) prior to reading this documentation.
:::

::: info
This tutorial is adapted only to the Linux and Mac environment.
:::

## Requirements
* First, [Vagrant](https://www.vagrantup.com/downloads.html) and [Virtualbox](https://www.virtualbox.org/) as a hypervisor,
 installed on your host.
* Second, you will need to create and configure your host machine using your [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/).
An example of `Vagrantfile` is provided in your `bonita-continuous-delivery_<version>/vagrant/` directory. You can find an
example for one machine or two.
* Third, you need to define your infrastructure and Bonita stack with a scenario. An example scenario file 
named **vagrant_single.yml.EXAMPLE** is provided in the **bonita-continuous-delivery_x.x.x/scenarios**. For more
information you can read the **[BCD Scenario reference](scenarios.md)**
* Finally, the virtual machine that you will create must have access to the Internet because some packages will be updated/installed.

## Step-by-step tutorial
Here is a complete example using the configuration files provided.

::: warning
This example uses an insecure private key. See [more](https://www.vagrantup.com/docs/vagrantfile/ssh_settings.html)... 
:::

Assuming you have a `bonita-continuous-delivery_2.0.0` directory in your `$HOME` directory.

### 1. Prepare and start your host
This step will create and configure a single virtual machine with an IP 192.168.10.10 defined in `Vagrantfile`
In your host machine:
```bash
$ cd $HOME/bonita-continuous-delivery_2.0.0/vagrant/1-machine/

$ vagrant up
Bringing machine 'bonita-stack' up with 'virtualbox' provider...
==> bonita-stack: Importing base box 'ubuntu/xenial64'...
[...]
==> bonita-stack: Setting up libpython-stdlib:amd64 (2.7.12-1~16.04) ...
==> bonita-stack: Setting up python (2.7.12-1~16.04) ...

$ VBoxManage list runningvms
"1-machine_bonita-stack_1525085617284_45644" {553ff7af-4d9a-4325-a902-ae17edce1e54}

``` 
### 2. Launch your BCD controller and deploy

```bash
$ docker run --rm -t -i --name bcd-controller \
-v $HOME/bonita-continuous-delivery_2.0.0/:/home/bonita/bonita-continuous-delivery \
-v ~/.vagrant.d/insecure_private_key:/home/bonita/.vagrant.d/insecure_private_key \
bonitasoft/bcd-controller /bin/bash

bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$
bonita@bcd-controller:~/bonita-continuous-delivery$bcd -s scenario/vagrant_single.yml stack deploy

```

### 3. Open bonita url
You should now be able to access Bonita Portal at  http://192.168.10.10:8081/bonita

## Cleaning up your environment

It is recommended to *undeploy* your Bonita application with the BCD controller like in the following:
```bash
$ bcd -y -s <path/to/scenario> stack undeploy
```
After, you can destroy your virtual machine running `vagrant destroy` command in the same path of your `Vagrantfile`.

::: info
You can read more about the Vagrant [CLI](https://www.vagrantup.com/docs/cli/) 
:::
