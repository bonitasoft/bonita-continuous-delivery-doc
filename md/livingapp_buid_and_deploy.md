TODO: lien vers cette page dans celle du deployer et du builder

# Build and deploy a Bonita living application

Bonita Continuous Delivery allows you to easily build and deploy a Bonita Living Applications on a running Bonita platform.
It uses the [Bonita Living Application Builder](livingapp_build.md) and the [Bonita Living Application Deployer](livingapp_deploy.md).

In the following, we present best pratices and various uses cases to let you easily build and deploy your Bonita Living
Application repository.

All examples assume that
  * you have clone the demo [Bonita Vacation Management example repository](https://github.com/bonitasoft/bonita-vacation-management-example)
GitHub repository as described in the [Bonita Living Application Builder](livingapp_build.md) documentation
  * you have already defined a scenario file called `build_and_deploy.yml` stored in the BCD `scenarios` folder
  * you already have a Bonita stack up and running, defined by the `build_and_deploy.yml` scenario file


##   Best practices about repositories

The _Bonita living application Builder_ is made to build an entire Bonita repository.  It is usually pretty fast, but depending on the size of your repository, the amount of artifacts to build, it can become longer. To avoid such situations, there is some best practices about Bonita repositories that you should respect.

### One repository per project
A Bonita repository should only contain artifacts related to a given project.
As example, if you want to use Bonita to build a _loan application_ and a _leave request application_, we strongly advise you to manage those two projects in two different repositories. It will allows you to build deploy and test each project separately easily using Bonita Continuous Delivery.
A repository should only contains the resources and the dependencies required to build and deploy the living applications of the project. Resources not used by the living applications should not be in the repository of the project.

### Use versioning to manage your history
During the life-cycle of a project, resources are brought to evolve. You may need to perform important changes on a process or on a Rest API extension for example. The best way to track the evolution is to use versioning.
Before to perform an important change on a resource, make sure to save the current state of your repository by creating a tag for example if you are using Git. Then you can increase the version of your resource and perform the change, knowing that you can easily do a rollback if needed.
The not recommended alternative would be to _duplicate_ the resource in your repository. If you start to manage your versions this way, your repository will getting bigger and bigger, and the time required by the _Bonita living application Builder_ to build your repository will increase. Moreover, it is really painful to work on a repository containing all the history of a project, it can lead to many confusions and errors during the development ( dependency issues ...).
The recommended way is definitively to  keep the latest version of each resources in the working repository, and the history on your Version Control System.
Since the version 7.7.0, the popular Version Control System _Git_ is integrated in Bonita. It is a fine way to manage properly the versioning of your projects.


##   Build and deploy straight

This is the simplest use case: you want to build a Bonita Living Application repository and deploy all associated resources.

To do so, you only have to pass the repository path to the build command and chain a deploy command without path parameter
like in the following:
```
$ bcd -y -s scenarios/build_and_deploy.yml livingapp build -p bonita-vacation-management-example deploy
```


## Build and select what to deploy

TODO:

* parler de quand il y a des trucs a faire entrte le build et le deploy.
* Exemple, rajouter des custom pages qui viennent de en dehors mais que l'on souhaite quand meme deployer.
* Selectionner uniquement une sous partie de ce qui a ete build pour y deployer ( en deplacant tout, en supprimant une partie des trucs buildes ...).
* choisir un titre plus fonctionnel plutot que de comment



Use case: you want to deploy resour

Examples:

deploy custom pages or custom themes developed out of a Bonita repository

do not deploy some artifacts present in the repository



Principles

build a Bonita repository with BCD

update the Application Archive resulting from the build

deploy the updated Application Archive



bcd -y -s scenarios/build_and_deploy.yml livingapp build -p bonita-vacation-management-example



copie d'une sous partie target: exemple, que le bdm

 cd bonita-vacation-management-example/target

unzip -d to-deploy bonita-vacation-management-example_20180411150630.zip

cd to-deploy



cas 1:  qu'une sous-partie, exemple que le bdm

bcd -y -s scenarios/build_and_deploy.yml livingapp deploy -p bonita-vacation-management-example/target/to-deploy/bdm



cas 2: tout sauf une partie, exemple pas les applications

bonita@832f613e56ea:~/bonita-continuous-delivery$ rm -rf bonita-vacation-management-example/target
target/       target_old/   target_old02/
bonita@832f613e56ea:~/bonita-continuous-delivery$ rm -rf bonita-vacation-management-example/target
target/       target_old/   target_old02/
bonita@832f613e56ea:~/bonita-continuous-delivery$ rm -rf bonita-vacation-management-example/target/to-deploy/applications/
bonita@832f613e56ea:~/bonita-continuous-delivery$ bcd -y -s scenarios/build_and_deploy.yml livingapp deploy -p bonita-vacation-management-example/target/to-deploy/



cas 3 : ajout de fichier







## Build and select how to deploy parts of the Application Archive

Create a deploy.json, build and deploy --> more control about how the Application is deployed
use policies

TODO: Meme objectif que avant, en gerant les policies. Attention: utilisation avancee, le format du .json peut etre ammene a evoluer dans les futures versions. -> peut etre dire la meme chose dans la page du deployer.



on conseille de stocker le deploy dans son repo

apres le build, unzip + faire une copie du fichier dans to-deploy

lancer la commande deploy sur le repertoire to-deploy