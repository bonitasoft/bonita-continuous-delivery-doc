# How to deploy a Living Application

This tutorial describes how to deploy a Bonita Living Application from the command line using BCD.


## Concepts

Prior to go to the `how to deploy` section, please read the following to understand the concepts used by the deployment
process.

### Introduction

Bonita living applications deployer (called `Deployer` in the following) is a tool allowing resources deployment to a running Bonita platform.

It is able to deploy

 - applications
 - business data model
 - business data model access controls
 - layouts
 - organizations
 - pages
 - processes
 - profiles
 - rest APi extensions
 - themes

Its entry point is called an `Application Archive` consisting of all artifacts that need to be deployed and an optional
file called `Deployment Descriptor`.


### Important notice

Prior deploying a Business Data Model, the Deployer put the Tenant in the  `paused`  state. It then restarts it after
deployment of the BDM. So a downtime of the related Bonita Tenant occurs in this case.


### Application Archive structure

The `Application Archive` is a folder or a zip containing
* all artifacts that need to be deployed
* a file, called `Deployment Descriptor`, describing
  * the type of the artifacts
  * the path to the artifacts in the archive
  * the way, called `Policy`, the artifacts will be deployed

The `Deployment Descriptor` is optional. If it is not provided, the `Deployer` first tries to generate it using the
following rules:
* the `Deployer` initializes a `Deployment Descriptor` empty instance in memory
* the `Application Archive` is a unzipped or copied in a temporary folder
* the `Deployer` lists all files in the folder
* for each file, it tries to guess the type of the resource. If it is a supported one, the `Deployer` adds it as a reference
in the descriptor and sets it a [default policy](#default-policies)
* finally, a `Deployment Descriptor` instance is associated to the `Application Archive` and used during subsequent processing

**Note**: if you provide several files related to a resource which is supposed to be single (for instance Business Data Model,
Organization), only one of the resources will be referenced. There is no guaranty about which file is kept so please avoid
this situation to ensure deployment reproducibility.


Here is an example of the `Application Archive` structure:
```
parentFolder
  -app
    -Application_Data.xml
    -page-myDashboard.zip
  -myResourceRestAPI-1.0.0-SNAPSHOT.zip
  -organization
    -ACME.xml
    -Profile_Data.xml
  deploy.json
```

### Deployment Descriptor file

The `Deployment Descriptor` file should
* Be a valid json file named `deploy.json` located at the root of the Application Archive
* Have the following attributes
  * an object for each resource types to be deployed
  * each resource is described by attributes
    * `file`: the relative path of the resource in the Application Archive
    * `policy`: the way the deployment of the resource is performed. This allows the `Deployer` to decide how to behave in case of the resource is already present in the targeted Bonita platform

Example of `Deployment Descriptor` file
```json
{
  "organization": {
    "file": "relative/path/to/organisationDescriptor.xml",
    "policy": "MERGE_DUPLICATES"
  },
  "profiles": [
    {
      "file": "relative/path/to/profilesDescriptor1.xml",
      "policy": "REPLACE_DUPLICATES"
    },
    {
      "file": "relative/path/to/profilesDescriptor2.xml",
      "policy": "REPLACE_DUPLICATES"
    }
  ],
  "processes": [
    {
      "file": "processes/New-Vacation-Request--1.0.bar",
      "policy": "IGNORE_DUPLICATES"
    },
    {
      "file": "processes/Initiate-Vacation-Available--1.0.bar"
    }
  ],
  "restAPIExtensions": [
    {
      "file": "relative/path/to/restAPIExtension1.zip",
    },
    {
      "file": "relative/path/to/restAPIExtension2.zip"
    }
  ],
  "pages": [
    {
      "file": "relative/path/to/customPage1.zip"
    },
    {
      "file": "relative/path/to/customPage2.zip"
    }
  ],
  "layouts": [
    {
      "file": "relative/path/to/customLayout1.zip"
    },
    {
      "file": "relative/path/to/customLayout2.zip"
    }
  ],
  "themes": [
      {
        "file": "relative/path/to/customTheme1.zip"
      },
      {
        "file": "relative/path/to/customTheme2.zip"
      }
    ],
  "applications": [
    {
      "file": "relative/path/to/applicationDescriptor1.xml",
      "policy": "REPLACE_DUPLICATES"
    },
    {
      "file": "relative/path/to/applicationDescriptor2.xml",
      "policy": "REPLACE_DUPLICATES"
    }
  ],
  "businessDataModel": {
    "file": "bdm/bdm.zip"
  },
  "bdmAccessControl": {
    "file": "bdm/bdm-access-control.xml"
  }
}
```

#### Supported Policies

* Applications:
  * `FAIL_ON_DUPLICATES`: deployment fails if the `Application` or `ApplicationPage` already exists
  * `REPLACE_DUPLICATES`: if the `Application` or `ApplicationPage` already exists, the deployer deletes the existing one
  and deploys the new one
* Organization:
  * `FAIL_ON_DUPLICATES`: if an item already exists, the deployment fails and is reverted to the previous state
  * `IGNORE_DUPLICATES`: existing items are kept
  * `MERGE_DUPLICATES`: existing items in the current organization are updated to have the values of the item in the
  imported organization
* Processes:
  * `FAIL_ON_DUPLICATES`: if the process already exists (same `name` and `version`), the deployment fails.
  * `IGNORE_DUPLICATES`: only deploys a process when it does not already exists (same `name` and `version`)
  * `REPLACE_DUPLICATES`: if the process already exists (same `name` and `version`), the deployer deletes the existing one
  and deploys the new one
* Profiles: `REPLACE_DUPLICATES` (In case of conflict on the name, it replaces completely the profile including profile
entries and profile mappings)


#### Default Policies

 * Applications: `REPLACE_DUPLICATES`
 * Organization: `MERGE_DUPLICATES`
 * Processes: `REPLACE_DUPLICATES`
 * Profiles: `REPLACE_DUPLICATES`
 
 
#### Implicit Policies:

The following artifacts are used with **implicit** policies, it means that you do not have to declare those policies in
the deployment descriptor file. There is no other policies available for those artifacts.
 * Rest API extensions: `REPLACE_DUPLICATES`
 * Pages: `REPLACE_DUPLICATES`
 * Layouts: `REPLACE_DUPLICATES`
 * Themes: `REPLACE_DUPLICATES`
 * Business Data Model: `REPLACE_DUPLICATES`
 * BDM access control: `REPLACE_DUPLICATES`



## Usage

The Bonita Continuous Delivery add-on allows you to deploy living applications artifacts using the Deployer, using the
following command:
```
bcd -s <scenario> --yes livingapp deploy -p <path>
```
Where
* \<scenario>_ is the path to the scenario to be used. It allows to deploy to the running Bonita Stack using the tenant
credentials defined in this file
* _\<path>_ is the file path of the application archive to deploy.


You can add a _-\-debug_ extra option to enable the log debug mode.  
You can refer to the [BCD command line documentation](bcd_cli.md) for a complete list of available options.
