# How to manage Living Apps Configuration

This tutorial describes how to configure your Bonita Living Application from the command line using BCD.

## How it works

Since BCD 3.0.0, if you build a living Application you will get separatly :
- a zip file which contains binaries
- a bconf file which contains the configuration for the target [environment](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/environments) (Local by default)

Here is an example of a build result :
```
bonita-vacation-management-example
├── target
   └── bonita-vacation-management-example-Qualification-20181003140237.zip
   └── bonita-vacation-management-example-Qualification-20181003140237.bconf
```

It's possible to extract the configuration to check it and also override (merge) some parameters if needed.

### Extract configuration

You can extract the configuration if you want to check it or modify it

```
bcd -y -s scenarios/euwest1_performance.yml livingapp extract-conf -p bonita-vacation-management-example/target/bonita-vacation-management-example-Qualification-20181003140237.bconf -o scenarios/euwest1_performance_Qualification.yml
```

The configuration looks like this :
```
---
processes:
- name: "Modify Pending Vacation Request"
  version: "1.4.1"
  parameters:
  - name: "calendarApplicationName"
    value: "Bonitasoft-NewVacationRequest/1.4.0"
    type: "String"
  - name: "calendarCalendarId"
    value: "mydomain.com_4gc5656x7f57cfsrejgb@group.calendar.google.com"
    type: "String"
```

As it may contain sensitive data, it's recommended to crypt your configuration using [vault](how_to_use_bcd_with_data_encrypted) :

```
ansible-vault encrypt scenarios/euwest1_performance_Qualification.yml
New Vault password:
Confirm New Vault password:
Encryption successful
```

You can also just verify if some parameters are missing for this environment :

```
bcd -y -s scenarios/euwest1_performance.yml livingapp extract-conf --without-value -p bonita-vacation-management-example/target/bonita-vacation-management-example-Qualification-20181003140237.bconf -o scenarios/euwest1_performance_Qualification_missing_parameters.yml
```

Notes :
- If you omit to specify -o, the output file named parameters.yml will be created in the same directory
- If all parameters are set, no file will be created.

### Merge configuration

You may want to complete or override some parameters coming from your Living App repository,
```
bcd -y -s scenarios/euwest1_performance.yml livingapp merge-conf -p bonita-vacation-management-example/target/bonita-vacation-management-example-Qualification-20181003140237.bconf -i scenarios/euwest1_performance_Qualification.yml -o /tmp/bonita-vacation-management-example-Qualification-20181003140237-modified.bconf
```

Note : the content of bconf file is not encrypted so it's recommended to clean them after usage.
