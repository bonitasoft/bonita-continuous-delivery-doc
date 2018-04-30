# How to deploy Bonita with Vagrant

BCD can not only deploy you Bonita Stack in the cloud. You can also deploy your stack on-premises hosts. 
On this page you will find usage examples and references about how to deploy your stack with Vagrant.

::: Warning
It is highly recommended to have a clear understanding of [Vagrant](https://www.vagrantup.com/intro/index.html) prior to reading this documentation.
:::

## Requirements
* First, [Vagrant](https://www.vagrantup.com/downloads.html) installed in your host.
* Second, you will need to create and configure your host machine using your [Vagrantfile](https://www.vagrantup.com/docs/vagrantfile/).
An example of `Vagrantfile` is provided in your `bonita-continuous-delivery_<version>/vagrant/1-machine` directory.
* Third, you need to define your infrastructure and Bonita stack with a scenario. An example scenario file 
named **vagrant_single.yml.EXAMPLE** is provided in the **bonita-continuous-delivery_<version>/scenarios**


## Step-by-step tutorial
Here is a complete example using the configuration files provided

::: Warning
This example uses an insecure private key... 
:::

Assuming you have a `bonita-continuous-delivery_2.0.0` directory in your `$HOME` directory and VirtualBox as hypervisor.

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
$ docker run --rm -t -i --name bcd-controller -v $HOME/bonita-continuous-delivery_2.0.0/:/home/bonita/bonita-continuous-delivery 
-v ~/.vagrant.d/insecure_private_key:/home/bonita/.vagrant.d/insecure_private_key bonitasoft/bcd-controller /bin/bash

bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$
bonita@bcd-controller:~/bonita-continuous-delivery$bcd -s scenario/vagrant_single.yml stack deploy

```

### 3. Open bonita url
You can access to your to your bonita application in http://192.168.10.10:8081/bonita
