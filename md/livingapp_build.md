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
$ git clone https://github.com/bonitasoft/bonita-vacation-management-example
```

This step creates a `bonita-continuous-delivery/bonita-vacation-management-example` directory which is the Living App repository we are going to build.


### 2. Provide the repository's build dependencies

The next step is to provide all dependencies required to build your repository. Hence the following artifatcs must be present in the `bonita-continuous-delivery/dependencies` directory:

**`bonita-la-builder-<bonita_version>-exec.jar`**  
The Bonita Living Application Builder library. Its version must correspond to the **`bonita_version`** variable defined in your BCD scenario. This is the version of the Bonita stack where generated artifacts will be deployed.  
For instance the `bonita-la-builder-7.8.0-exec.jar` file must be present to build artifacts meant to be deployed on a Bonita `7.8.0` stack.

**`bonita-sp-*-maven-repository.zip`**  
Bonita Maven repository zip files if your repository contains [REST API extensions](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/api-extensions). The version of the Maven repository zip must correspond to the `bonita.version` property defined in your REST API extension's `pom.xml`.


### 3. Build the repository

BCD is now ready to build your repository using the `bcd livingapp build` command. From your BCD Controller container, run these commands:

```
$ cd bonita-continuous-delivery
$ bcd -s scenarios/euwest1_performance.yml --yes livingapp build -p bonita-vacation-management-example -e Qualification
```

The build process finally generates a zip package located in the repository's `target` repository. The generated archive is named after the repository's root directory name.  
For instance with the `bonita-vacation-management-example` example, a `bonita-vacation-management-example/target/bonita-vacation-management-example_<timestamp>.zip` file is generated.

::: info
Refer to the [BCD Command-line reference](bcd_cli.md) for a complete list of available options for the `bcd livingapp build` command.
:::


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
In this example, the REST API extension has a dependency on Bonita 7.6.3.

**On the control host**
```
$ cd bonita-continuous-delivery_2.0.0

$ git clone https://github.com/bonitasoft/bonita-vacation-management-example
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
        <bonita.version>7.6.3</bonita.version>

$ ls -nh dependencies 
total 94M
-rw-r--r-- 1 1000 1000  85M Mar 29 12:00 bonita-la-builder-7.8.0-exec.jar
-rw-rw-r-- 1 1000 1000 8,7M Mar 29 16:16 bonita-sp-7.6.3-maven-repository.zip
-rw-r--r-- 1 1000 1000  157 Mar 29 11:57 README.md

$ grep "bonita_version" scenarios/euwest1_performance.yml
bonita_version: 7.8.0
```


**In the BCD controller container**
```
bonita@bcd-controller:~$ cd bonita-continuous-delivery
bonita@bcd-controller:~/bonita-continuous-delivery$ bcd -s scenarios/euwest1_performance.yml --yes livingapp build -p bonita-vacation-management-example -e Qualification

bonita@bcd-controller:~/bonita-continuous-delivery$ ls -nh bonita-vacation-management-example/target 
total 8,1M
drwxr-xr-x 9 1000 1000 4,0K Mar 29 16:29 bonita-vacation-management-example
-rw-r--r-- 1 1000 1000 8,1M Mar 29 16:29 bonita-vacation-management-example_20180329162901.zip
drwxr-xr-x 3 1000 1000 4,0K Mar 29 16:29 bpmn
drwxr-xr-x 2 1000 1000 4,0K Mar 29 16:29 generated-jars
drwxr-xr-x 3 1000 1000 4,0K Mar 29 16:29 ui-designer
```
