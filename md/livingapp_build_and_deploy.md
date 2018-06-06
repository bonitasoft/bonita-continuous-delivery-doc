# How to build and deploy Living Apps (Best Practices)

Bonita Continuous Delivery enables you to easily build and deploy a Bonita Living Applications on a running Bonita platform.
It uses the [Bonita Living Application Builder](livingapp_build.md) and the [Bonita Living Application Deployer](livingapp_deploy.md).

In the following, we present best practices and various uses cases to let you easily build and deploy your Bonita Living Application repository.

All examples assume that:
  * you have cloned the demo [Bonita Vacation Management example repository](https://github.com/bonitasoft/bonita-vacation-management-example)
GitHub repository as described in the [Bonita Living Application Builder](livingapp_build.md) documentation
  * you have already defined a scenario file called `build_and_deploy.yml` stored in the BCD `scenarios` directory
  * you already have a Bonita stack up and running, defined by the `build_and_deploy.yml` scenario file

If you want to deploy into a Bonita platform running on an existing machine, you can find an example of a scenario file `bonita-continuous-delivery_2.0.0/scenario/build_and_deploy.yml.EXAMPLE` 
with its inventory file in `bonita-continuous-delivery_2.0.0/ansible/inventory/static-inventory/livingapp-build-deploy-static-inventory.EXAMPLE`.
 
## Best practices about repositories

The _Bonita Living Application Builder_ is made to build an entire Bonita repository.  
It is usually pretty fast, but depending on the size of your repository, the amount of artifacts to build, it can become longer. To avoid such situations, there is some best practices about Bonita repositories that you should respect.

### One repository per project

A Bonita repository should only contain artifacts related to a given project.
For example, if you want to use Bonita to build a _loan application_ and a _leave request application_, we strongly advise you to manage those two projects in two different repositories. It will allow you to build, deploy and test each project separately easily using Bonita Continuous Delivery.  

A repository should only contain the resources and the dependencies required to build and deploy the Living Applications of the project. Resources not used by the Living Applications should not be in the repository of the project.

### Use versioning to manage your history

During the life-cycle of a project, resources are brought to evolve. You may need to perform important changes on a process or on a Rest API extension for example. The best way to track the evolution is to use versioning.  

Before to perform an important change on a resource, make sure to save the current state of your repository by creating a tag for example if you are using Git. Then you can increase the version of your resource and perform the change, knowing that you can easily do a rollback if needed.  

**We do not recommend** to _duplicate_ the resource in your repository. If you start to manage your versions this way, your repository will get bigger and bigger, and the time required by the _Bonita Living Application Builder_ to build your repository will increase. Moreover, it is really painful to work on a repository containing all the history of a project, it can lead to many confusions and errors during the development ( dependency issues ...).  

**The recommended way** is definitively to keep the latest version of each resources in the working repository, and the history on your Version Control System.  
Since the version 7.7.0 of Bonita, the popular Version Control System _Git_ is integrated in Bonita Studio. It is a fine way to manage properly the versioning of your projects. The Bonita documentation provides an example on [how to share a repository on GitHub](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/share-a-repository-on-github).


## Build and deploy straight

This is the simplest use case: you want to build a Bonita repository and deploy all associated resources.

To do so, you only have to pass the repository path to the build command and chain a deploy command without path parameter
like in the following:
```
$ bcd -y -s <path/to/scenario> livingapp build -p <path/to/bonita/repository> deploy
```

This command will first build the Bonita repository. The output of the build is a zip file. It is located in the _target_ directory, created by the builder. This zip file corresponds to the _Application Archive_ used by the deployer. It contains all the resources built from the input repository.  

In a second time, the deployer deploys all those resources on the running Bonita platform. 

## Build and select what to deploy

Sometimes, you may need to perform some actions between the build phase and the deploy phase. You may want to deploy only a subpart of the repository that has been built, or add some external resources in the application archive to deploy. Here are some hints on how to do it.  
The idea is always the same:

 - **Build** a Bonita repository with BCD
 - **Update** the application archive created by the builder
 - **Deploy** the updated application archive

First, we build the Bonita repository:
```
bcd -y -s <path/to/scenario> livingapp build -p <path/to/bonita/repository>
```
The build operation creates a **target** directory in the Bonita repository. Let's move to this directory:
```
cd <path/to/bonita/repository>/target
```
This directory contains built artifacts, including the application archive we want to update. Keep in mind that the deployer can take a zip file or a directory as input. So, we are going to unzip the raw application archive, in a _to-deploy_ directory:
```
unzip -d to-deploy <bonita-repository-name>_<timestamp>.zip
```
A new directory _to-deploy_ has been createda and it contains all the built artifacts from your Bonita repository.  

You can perform some actions on the content of this directory to customize your deployment. Here are some use cases:

#### Example 1: I only want to deploy the Business Data Model from my Bonita repository

In this example, we only want to deploy the BDM. The _to-deploy_ directory should contain a subfolder _bdm_. We simply need to tell the deployer to deploy this subfolder as follows:
```
bcd -y -s <path/to/scenario> livingapp deploy -p <path/to/bonita/repository>/target/to-deploy/bdm
```

#### Example 2: I only want to deploy all the resources from my Bonita repository except applications

The _to-deploy_ directory should contain a subfolder _applications_. We simply need to delete this subfolder, then tell the deployer to deploy the _to-deploy_ directory as follows:
```
rm -rf <path/to/bonita/repository>/target/to-deploy/applications/
bcd -y -s <path/to/scenario> livingapp deploy -p <path/to/bonita/repository>/target/to-deploy
```

#### Example 3: I want to add some custom pages in the resources to deploy

This use case is a bit more advanced. Imagine that your Bonita repository is stored on a given Git repository and your custom pages used by your applications in an other Git repository. You will have to build your Bonita repository as described above, then to retrieve your custom pages (Git clone...) and to add them to the application archive to deploy. 


## Build and select how to deploy parts of the Application Archive

As described in the [Bonita Living Application Deployer documentation](livingapp_deploy.md), it is possible to pass to the deployer a _deployment descriptor file_. This file must be called _deploy.json_ and be at the root of the application archive.  
The deployment descriptor file is used to specify which resources have to be deployed, and with which policy. It is an alternative way to select a sub part of the application archive to deploy.  
An interesting way to use it is to store some deployment descriptor files in your Bonita repository, one for each 'build and deploy' scenario you want to perform. Between the build and the deploy  phase, just move the fine deployment descriptor at the root of your application archive, as described above.  
::: warning
The format of the deployment descriptor file is not friendly to use. It might evolve in further versions.
:::