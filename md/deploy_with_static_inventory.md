# How to deploy with a static inventory

BCD lets you deploy a Bonita stack on your existing hosts using a **static inventory**.  
On this page, you will find detailed information about the usage of BCD with your existing hosts. We will also provide an example creating virtual machines with [Vagrant](https://www.vagrantup.com/) and deploying a Bonita stack with BCD.

## Preparing configuration files

There are two main files to configure: an **Ansible inventory** and a **BCD scenario**.

### Inventory

This file defines the hosts and groups of hosts upon which BCD will operates. The preferred location for inventory files is the `ansible/inventory` directory.

In particular, you will find examples of static inventory files in the `static-inventory` subdirectory.

::: info
Refer to [Ansible's documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) for further details about Ansible inventories.
:::

### Scenario

Scenarios follow the same rules as described in the [Scenario file reference documentation](scenarios.md). There are five mandatory variables to configure while using BCD with static inventories:
* `bcd_provider`: Infrastructure provider. It has to be set with `static_inventory`
* `ansible_ssh_user`: The ssh user to use.
* `ansible_ssh_private_key_file`: Private key file used by ssh.
* `bcd_inventory`: Ansible inventory path required when `bcd_provider` is `static_inventory`
* `configuration`: Configuration filename used to group variables for common deployment types.

An example scenario is provided with the `scenarios/static_inventory.yml.EXAMPLE` file.


## Example with Vagrant

This section describes a complete example of Bonita stack deployment on existing virtual machines created with Vagrant.

::: warning
For this tutorial, it is highly recommended to have a clear understanding of [Vagrant](https://www.vagrantup.com/intro/index.html).
:::

::: info
This tutorial is adapted to Linux and Mac environments only.
:::

### Requirements

* First, [Vagrant](https://www.vagrantup.com/downloads.html) and [Virtualbox](https://www.virtualbox.org/) as a hypervisor must be installed on your host.
* Second, you will need to create and configure your host machine using your [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/). An example of `Vagrantfile` is provided in the `vagrant` directory. You can find an
example for one or two machines infrastructure.
* Third, you need to define your infrastructure and Bonita stack with a scenario. An example scenario file named **vagrant_single.yml.EXAMPLE** is provided in the **scenarios** directory. For more information you can read the **[BCD Scenario reference](scenarios.md)**.
* Finally, the virtual machine that you will create must have access to the Internet because some packages will be updated/installed.

### Step-by-step tutorial

Here is a complete example using the configuration files provided.

::: warning
This example uses an insecure private key. See [more](https://www.vagrantup.com/docs/vagrantfile/ssh_settings.html)...
:::

Assuming you have a `bonita-continuous-delivery_3.0.0` directory in your `$HOME` directory.

#### 1. Prepare and start your target virtual machine

This step will create and configure a single virtual machine with an IP **192.168.10.10** defined in `Vagrantfile`.  

On your host machine:
```bash
$ cd $HOME/bonita-continuous-delivery_3.0.0/vagrant/1-machine/

$ vagrant up
Bringing machine 'bonita-stack' up with 'virtualbox' provider...
==> bonita-stack: Importing base box 'ubuntu/xenial64'...
[...]
==> bonita-stack: Setting up libpython-stdlib:amd64 (2.7.12-1~16.04) ...
==> bonita-stack: Setting up python (2.7.12-1~16.04) ...

$ VBoxManage list runningvms
"1-machine_bonita-stack_1525085617284_45644" {553ff7af-4d9a-4325-a902-ae17edce1e54}
``` 

#### 2. Launch your BCD controller and deploy Bonita stack

Copy the **vagrant_single.yml.EXAMPLE** to a **scenarios/vagrant_single.yml** file. Edit your scenario as necessary and start a BCD controller.

```bash
$ docker run --rm -ti --hostname bcd-controller --name bcd-controller \
        -v ~/bonita-continuous-delivery_3.0.0:/home/bonita/bonita-continuous-delivery \
        -v ~/.vagrant.d/insecure_private_key:/home/bonita/.vagrant.d/insecure_private_key \
        quay.io/bonitasoft/bcd-controller /bin/bash
bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$
bonita@bcd-controller:~/bonita-continuous-delivery$bcd -s scenarios/vagrant_single.yml stack deploy
```

#### 3. Open Bonita URL

You should now be able to access Bonita Portal at **http://192.168.10.10:8081/bonita**.

#### 4. Cleaning up your environment

Before destroying your virtual machines, it is recommended to *undeploy* your Bonita stack with BCD as follows:

```bash
$ bcd -s <path/to/scenario> -y stack undeploy
```

Then you may destroy your virtual machine with the `vagrant destroy` command.

::: info
You can read more about the Vagrant CLI on [Vagrant documentation site](https://www.vagrantup.com/docs/cli/).
:::
