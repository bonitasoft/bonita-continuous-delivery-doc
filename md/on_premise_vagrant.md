# How to deploy Bonita with Vagrant

BCD can not only deploy your Bonita Stack in the cloud. You can also deploy your stack on-premises hosts. 
On this page you will find usage examples and references about how to deploy your stack with Vagrant.

::: Attention
It is highly recommended to have a clear understanding of [Vagrant](https://www.vagrantup.com/intro/index.html) prior to reading this documentation.
:::

::: Notice
This tutorial is adapted only to the Linux and Mac environment.
:::

## Requirements
* First, [Vagrant](https://www.vagrantup.com/downloads.html) and [Virtualbox](https://www.virtualbox.org/) as a hypervisor,
 installed on your host.
* Second, you will need to create and configure your host machine using your [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/).
An example of `Vagrantfile` is provided in your `bonita-continuous-delivery_<version>/vagrant/1-machine` directory.
* Third, you need to define your infrastructure and Bonita stack with a scenario. An example scenario file 
named **vagrant_single.yml.EXAMPLE** is provided in the **bonita-continuous-delivery_<version>/scenarios**. For more 
information you can read the **[BCD Scenario reference](scenarios.md)**
* Finally, the virtual machine that you will create has must be able to access to the Internet because some packages will be updated/installed.

## Step-by-step tutorial
Here is a complete example using the configuration files provided

::: Attention
This example uses an insecure private key. See [more](https://www.vagrantup.com/docs/vagrantfile/ssh_settings.html)... 
:::

Assuming you have a `bonita-continuous-delivery_1.0.3` directory in your `$HOME` directory.

### 1. Prepare and start your host
This step will create and configure a single virtual machine with an IP 192.168.10.10 defined in `Vagrantfile`
In your host machine:
```bash
$ cd $HOME/bonita-continuous-delivery_1.0.3/vagrant/1-machine/

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
$ docker run --rm -t -i --name bcd-controller -v $HOME/bonita-continuous-delivery_1.0.3/:/home/bonita/bonita-continuous-delivery 
-v ~/.vagrant.d/insecure_private_key:/home/bonita/.vagrant.d/insecure_private_key bonitasoft/bcd-controller /bin/bash

bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$
bonita@bcd-controller:~/bonita-continuous-delivery$bcd -s scenario/vagrant_single.yml deploy

```

### 3. Open bonita url
You can access to your to your bonita application in http://192.168.10.10:8081/bonita

## Cleaning up your environment

It is recommended to *undeploy* your Bonita application with the BCD controller like in the following:
```bash
$ bcd -y -s <path/to/scenario> undeploy
```
After, you can destroy your virtual machine running `vagrant destroy` command in the same path of your `Vagrantfile`.

::: Info
You can read more about the Vagrant [CLI](https://www.vagrantup.com/docs/cli/) 
:::
