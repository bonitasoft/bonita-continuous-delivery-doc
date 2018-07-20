# How to deploy Living App artifacts

This tutorial describes how to deploy Bonita Living Application artifacts from the command line using BCD.


## How it works

This section provides detailed information of Bonita artifacts deployment with BCD.


### Deployment concepts

First, here are the **types of artifacts** that can be deployed:
* applications
* business data model
* business data model access controls
* layouts
* organizations
* pages
* processes
* profiles
* REST API extensions
* themes

The deployment entry point is called an **Application Archive**. It consists of all artifacts to be deployed and an optional configuration file called **Deployment Descriptor**. This file describes which **Policy** should be applied while deploying each artifact.


### Application Archive structure

The Application Archive can be a directory or a zip file. It may contain a Deployment Descriptor in the form of a `deploy.json` file. If not provided, then a Deployment Descriptor is generated in-memory using the following rules:
* the type of artifact is determined from the nature of each file
* a [default policy](#supported-policies) is applied for each supported artifact to deploy

Here is an example of Application Archive structure:
```
bonita-vacation-management-example
├── applications
│   └── Application_Data.xml
├── bdm
│   └── bdm.zip
├── deploy.json
├── extensions
│   └── tahitiRestApiExtension-1.0.0.zip
├── organizations
│   └── ACME.xml
├── pages
│   └── page_ExampleVacationManagement.zip
├── processes
│   ├── Cancel Vacation Request--1.4.1.bar
│   ├── Initiate Vacation Available--1.4.1.bar
│   ├── Modify Pending Vacation Request--1.4.1.bar
│   ├── New Vacation Request--1.4.1.bar
│   └── Remove All Business Data--1.4.1.bar
└── profiles
    └── default_profile.xml
```

::: warning
**Note**: if you provide several artifacts for a resource which is supposed to be single (for instance Business Data Model, Organization), only one of the artifacts will be deployed. There is no guaranty about which file is kept so please avoid this situation to ensure deployment reproducibility.
:::

### Deployment Descriptor file

The Deployment Descriptor file must be a valid JSON file named **`deploy.json`** and it must be located at the root of the Application Archive.

Each artifact to deploy must be defined with the following attributes:
* `file`: (Mandatory) the relative path to the artifact in the Application Archive
* `policy`: (Optional) the name of the policy to apply in case the same artifact is already present in the target Bonita platform. If omitted, then the [default policy](#supported-policies) of the artifact's type will be applied.

**Example of Deployment Descriptor file**
```json
{
  "organization": {
    "file": "organizations/ACME.xml",
    "policy": "MERGE_DUPLICATES"
  },
  "profiles": [
    {
      "file": "profiles/default_profile.xml",
      "policy": "REPLACE_DUPLICATES"
    },
    {
      "file": "profiles/custom_profile.xml",
      "policy": "REPLACE_DUPLICATES"
    }
  ],
  "processes": [
    {
      "file": "processes/New Vacation Request--1.4.1.bar",
      "policy": "IGNORE_DUPLICATES"
    },
    {
      "file": "processes/Initiate Vacation Available--1.4.1.bar"
    }
  ],
  "restAPIExtensions": [
    {
      "file": "extensions/tahitiRestApiExtension-1.0.0.zip",
    }
  ],
  "pages": [
    {
      "file": "pages/page_ExampleVacationManagement.zip"
    }
  ],
  "layouts": [
    {
      "file": "layouts/customLayout1.zip"
    },
    {
      "file": "layouts/customLayout2.zip"
    }
  ],
  "themes": [
      {
        "file": "themes/customTheme1.zip"
      },
      {
        "file": "themes/customTheme2.zip"
      }
    ],
  "applications": [
    {
      "file": "applications/Application_Data.xml",
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

### Supported Policies

<div id="supported-policies">

* Applications:
  * `FAIL_ON_DUPLICATES`: deployment fails if the `Application` or `ApplicationPage` already exists
  * `REPLACE_DUPLICATES`: **(default)** if the `Application` or `ApplicationPage` already exists, the existing one is deleted and the new one is deployed
* Organization:
  * `FAIL_ON_DUPLICATES`: if an item already exists, the deployment fails and is reverted to the previous state
  * `IGNORE_DUPLICATES`: existing items are kept
  * `MERGE_DUPLICATES`: **(default)** existing items in the current organization are updated to have the values of the item in the imported organization
* Processes:
  * `FAIL_ON_DUPLICATES`: if the process already exists (same `name` and `version`), the deployment fails
  * `IGNORE_DUPLICATES`: only deploys a process when it does not already exist (same `name` and `version`)
  * `REPLACE_DUPLICATES`: **(default)** if the process already exists (same `name` and `version`), the existing one is deleted and the new one is deployed. As a reminder, deleting a process means: disable the process, delete all related cases and delete the process

The following artifacts are used with **implicit policies**. It means that you do not have to declare those policies in the Deployment Descriptor file. There is no other policy available for those artifacts.
* Business Data Model: `REPLACE_DUPLICATES`
* BDM access control: `REPLACE_DUPLICATES`
* Layouts: `REPLACE_DUPLICATES`
* Pages: `REPLACE_DUPLICATES`
* Profiles: `REPLACE_DUPLICATES`
* REST API extensions: `REPLACE_DUPLICATES`
* Themes: `REPLACE_DUPLICATES`

</div>


### Caveats

* `FAIL` policy implies that the deployment stops right after the failure meaning that subsequent elements of the deployment are not deployed at all.
* Prior to deploying a Business Data Model, [the Bonita tenant is paused](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/pause-and-resume-bpm-services). So a downtime of the tenant occurs. The tenant is resumed after the deployment of the BDM.
* REST API extension authorizations are not configured as part of the deployment process. They have to be configured while provisioning the Bonita platform. See [how to configure REST API authorization](how_to_configure_rest_api_authorization.md) with BCD.


## How to use

Use the `bcd livingapp deploy` command to deploy Living App artifacts:
```
bcd -s <scenario> livingapp deploy -p <path>
```
where:
* **\<scenario>** is the path to the BCD scenario which defines the target Bonita stack. Artifacts will be deployed using tenant credentials defined by this scenario (`bonita_tenant_login` and `bonita_tenant_password` variables).
* **\<path>** is the path to the Application Archive to deploy (file or directory).

You can add a **--debug** option to enable debug mode and increase verbosity.

::: info
Refer to the [BCD Command-line reference](bcd_cli.md) for a complete list of available options for the `bcd livingapp deploy` command.
:::


**Complete example:**

Here is how to deploy artifacts of the [Bonita Vacation Management example Living App](https://github.com/bonitasoft/bonita-vacation-management-example).

Assuming that:
* artifacts have been built and that a `bonita-vacation-management-example_20180329162901.zip` Application Archive zip file has been generated in the `bonita-vacation-management-example/target` directory
* a Bonita stack is up and running as defined in a `scenarios/euwest1_performance.yml` scenario file

_In the BCD controller container_:
```
bonita@bcd-controller:~$ cd bonita-continuous-delivery

bonita@bcd-controller:~/bonita-continuous-delivery$ ls -nh bonita-vacation-management-example/target 
total 8,1M
drwxr-xr-x 9 1000 1000 4,0K Mar 29 16:29 bonita-vacation-management-example
-rw-r--r-- 1 1000 1000 8,1M Mar 29 16:29 bonita-vacation-management-example_20180329162901.zip
drwxr-xr-x 3 1000 1000 4,0K Mar 29 16:29 bpmn
drwxr-xr-x 2 1000 1000 4,0K Mar 29 16:29 generated-jars
drwxr-xr-x 3 1000 1000 4,0K Mar 29 16:29 ui-designer
```

Then artifacts can be deployed using the generated zip file as follows:

```
bonita@bcd-controller:~/bonita-continuous-delivery$ bcd -s scenarios/euwest1_performance.yml --yes livingapp deploy -p bonita-vacation-management-example/target/bonita-vacation-management-example_20180329162901.zip
```

Artifacts can also be deployed providing the Application Archive directory as follows:

```
bonita@bcd-controller:~/bonita-continuous-delivery$ bcd -s scenarios/euwest1_performance.yml --yes livingapp deploy -p bonita-vacation-management-example/target/bonita-vacation-management-example
```
