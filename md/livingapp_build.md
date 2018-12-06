# How to build a Living App repository

This tutorial describes how to build a Bonita Living Application repository from the command line using BCD.

::: info
A Living Application repository (or **Living App repository**) contains the artifacts developed in Bonita Studio and the UI Designer. Its content is further described in [Bonita's Workspaces and repositories documentation](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/workspaces-and-repositories) page.
:::

The repository build process allows to generate Bonita deployable artifacts (for instance process `.bar` files, UI Designer pages `.zip` files, REST API extensions `.zip` files).


## Step-by-step tutorial

### 1. Prepare the repository

A Living App repository can be created using Bonita Studio and it can be [shared with Git or SVN](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/workspaces-and-repositories#toc5).

So first ensure you have your Living App repository available on your filesystem.  
For this tutorial, we will use the [Bonita Vacation Management example repository](https://github.com/bonitasoft/bonita-vacation-management-example) publicly available on GitHub.

```
$ cd bonita-continuous-delivery
$ git clone -b dev/7.8.0 https://github.com/bonitasoft/bonita-vacation-management-example
```

This step creates a `bonita-continuous-delivery/bonita-vacation-management-example` directory which is the Living App repository we are going to build. This example checks out the `dev/7.8.0` Git branch.


### 2. Provide the repository's build dependencies

The next step is to provide all dependencies required to build your repository, see [Common Installation Steps](getting_started.md#toc1). 

Hence the following artifacts must be present in the `$BCD_HOME/dependencies` directory or any of its subdirectories:

**`bonita-la-builder-<bonita_version>-exec.jar`**  
The Bonita Living Application Builder library. Its version must correspond to the **`bonita_version`** variable defined in your BCD scenario. This is the version of the Bonita stack where generated artifacts will be deployed.  
For instance the `bonita-la-builder-7.8.0-exec.jar` file must be present to build artifacts meant to be deployed on a Bonita `7.8.0` stack.

**`bonita-sp-*-maven-repository.zip`**  
Bonita Maven repository zip files if your repository contains [REST API extensions](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/api-extensions). The version of the Maven repository zip must correspond to the `bonita.version` property defined in your REST API extension's `pom.xml`.


### 3. Build the repository

BCD is now ready to build your repository using the `bcd livingapp build` command. From your BCD Controller container, run these commands:

```
$ cd bonita-continuous-delivery
$ bcd -s scenarios/build_and_deploy.yml --yes livingapp build -p bonita-vacation-management-example -e Test
```

The build process finally generates a **.zip** package and a **.bconf** artifact located in the repository's `target` repository. The generated artifacts are named after the repository's root directory name and the selected environment name.

For instance with the `bonita-vacation-management-example` example, the following files will be generated:
- `bonita-vacation-management-example/target/bonita-vacation-management-example-<environment>-<timestamp>.zip`
- `bonita-vacation-management-example/target/bonita-vacation-management-example-<environment>-<timestamp>.bconf`

::: info
Since BCD 3.0.0, process configurations are built into a separate **.bconf** artifact. This configuration artifact allows to separate process configuration (parameters) from application assets.  
As a result, the same **.zip** package can be deployed on all environments (Development, Test, Staging) whereas a specific **.bconf** artifact may be applied to each environment.
:::

Refer to the [BCD Command-line reference](bcd_cli.md) for a complete list of available options for the `bcd livingapp build` command.


## Repository vs. Builder version compatibility

The version of your repository **must exactly match** the version of the builder library.

If versions do not match, the `bcd livingapp build` command will exit with such a message:
```
[ERROR] Project version (7.8.0) does not match builder version (7.7.4)
[13:46:34.469] ERROR: Aborting! Command <livingapp-build> returned non-zero exit code <1>
```

The `bcd livingapp build` command does not allow to migrate your repository to the builder's version. Your repository has to be migrated to the appropriate version using [Bonita Studio](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/workspaces-and-repositories#toc6).


## Complete example

Here is a complete example of how to build the [Bonita Vacation Management example repository](https://github.com/bonitasoft/bonita-vacation-management-example) for Bonita version 7.8.0.  
In this example, the REST API extension has a dependency on Bonita 7.7.0.

**On the control host**
```
$ cd bonita-continuous-delivery_3.0.0

$ git clone -b dev/7.8.0 https://github.com/bonitasoft/bonita-vacation-management-example
$ ls -nh bonita-vacation-management-example
total 60K
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 applications
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 bdm
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 diagrams
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 environements
drwxrwxr-x  2 1000 1000 4,0K Mar 29 23:29 lib
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 organizations
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 process_configurations
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 profiles
-rw-rw-r--  1 1000 1000  486 Mar 29 16:17 README.md
drwxrwxr-x  3 1000 1000 4,0K Mar 29 16:17 restAPIExtensions
drwxr-xr-x  6 1000 1000 4,0K Mar 29 23:29 target
drwxr-xr-x  2 1000 1000 4,0K Mar 29 16:19 web_fragments
drwxrwxr-x 11 1000 1000 4,0K Mar 29 16:19 web_page
drwxr-xr-x 26 1000 1000 4,0K Mar 29 16:19 web_widgets
drwxrwxr-x  2 1000 1000 4,0K Mar 29 16:17 xsd

$ grep "<bonita.version>" bonita-vacation-management-example/restAPIExtensions/tahitiRestApiExtension/pom.xml
        <bonita.version>7.7.0</bonita.version>

$ ls -nhR dependencies/
dependencies/:
total 8
drwxr-xr-x    2 1000        1000           4.0K Nov 22 16:54 7.7.0
drwxr-xr-x    2 1000        1000           4.0K Nov 22 16:52 7.8.0

dependencies/7.7.0:
total 95368
-rw-r--r--    1 1000        1000            297 Nov 14 11:29 LICENSE
-rw-r--r--    1 1000        1000          84.5M Nov 14 11:29 bonita-la-builder-7.7.0-exec.jar
-rw-r--r--    1 1000        1000           8.6M Nov 14 11:29 bonita-sp-7.7.0-maven-repository.zip

dependencies/7.8.0:
total 106096
-rw-r--r--    1 1000        1000            297 Nov 13 14:03 LICENSE
-rw-r--r--    1 1000        1000          84.4M Nov 13 14:03 bonita-la-builder-7.8.0-exec.jar
-rw-r--r--    1 1000        1000          19.2M Nov 13 14:03 bonita-sp-7.8.0-maven-repository.zip


$ grep "bonita_version" scenarios/build_and_deploy.yml
bonita_version: 7.8.0
```


**In the BCD controller container**
```
bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$ bcd -s scenarios/build_and_deploy.yml --yes livingapp build -p bonita-vacation-management-example -e Test

bonita@bcd-controller:~/bonita-continuous-delivery$ ls -nh bonita-vacation-management-example/target 
total 9,0M
drwxr-xr-x 9 1000 1000 4,0K Dec   6 13:59 bonita-vacation-management-example
-rw-r--r-- 1 1000 1000 2,4K Dec   6 13:59 bonita-vacation-management-example-Test-20181206125838.bconf
-rw-r--r-- 1 1000 1000 9,0M Dec   6 13:59 bonita-vacation-management-example-Test-20181206125838.zip
drwxr-xr-x 3 1000 1000 4,0K Dec   6 13:59 bpmn
drwxr-xr-x 3 1000 1000 4,0K Dec   6 13:58 configurations
drwxr-xr-x 2 1000 1000 4,0K Dec   6 13:58 generated-jars
drwxr-xr-x 3 1000 1000 4,0K Dec   6 13:58 ui-designer
```
