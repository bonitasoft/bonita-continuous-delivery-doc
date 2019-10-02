# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.1.3 (2019-10-03)

### Bugfixes

* BCD-371 Custom theme build fails with BCD controller  
  This bugfix makes **NodeJS v10.16.3** and **NPM 6.9.0** specific versions available to the BCD controller. Therefore make sure your custom themes reference these versions in their Maven `pom.xml` as follows:
  ```xml
  [...]
  <properties>
    <node.version>v10.16.3</node.version>
    <npm.version>6.9.0</npm.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  [...]
  ```
  Further information may be found in the [Living App build documentation](https://documentation.bonitasoft.com/bcd/${varVersion}/livingapp_build#toc1).

## What's new in 3.1.2 (2019-08-01)

### Bugfixes

* BR-144 `livingapp deploy` may fail to deploy against a clustered Bonita runtime due to incorrect Cookie parsing
* BCD-348 `livingapp deploy` fails when tenant credentials contains ';' special character


## What's new in 3.1.1 (2019-06-13)

### New features

* Starting with release 3.1.1, BCD is compatible with **Bonita 7.9.x**.  
<span class="label label-danger">With Bonita 7.9.0 onwards</span> You must use one of these [license types in your scenarios](https://documentation.bonitasoft.com/bcd/${varVersion}/scenarios#toc4): `lic_type` in [`production`\|`qualification`\|`trial`]  
(starting from Bonita 7.9.0, `development` licenses can only be used with Bonita Studio).
* **Bonita 7.9.x** onwards runs on Ubuntu 18.04 base Docker image with Java 11 when deployed with BCD.

### Bugfixes

* BCD-339 Jenkins example `bcd-pipeline` fails when processes do not have parameters
* BCD-342 `bcd livingapp build` fails to unzip maven dependencies twice


## What's new in 3.1.0 (2019-04-16)

### New features

* Configurable HTTP timeout for Living Apps deployment (default to 120 seconds)

### Bugfixes

* BCD-340 DS min and max pool sizes are not configured

### Enhancements

* BCD controller is now using alpine 3.9
