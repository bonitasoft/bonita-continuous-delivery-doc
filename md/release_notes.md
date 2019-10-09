# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.2.0 (2019-10-11)

### Bugfixes

* BCD-371 Custom theme build fails with BCD controller  
  This bugfix makes **NodeJS ${nodeVersion}** and **NPM ${npmVersion}** specific versions available to the BCD controller. Therefore make sure your custom themes reference these versions in their Maven `pom.xml` as follows:
  ```xml
  [...]
  <properties>
    <node.version>${nodeVersion}</node.version>
    <npm.version>${npmVersion}</npm.version>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  [...]
  ```
  Further information may be found in the [Living App build documentation](https://documentation.bonitasoft.com/bcd/${varVersion}/livingapp_build#toc1).
