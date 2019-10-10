# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.2.0 (2019-10-11)

### New features

* BCD-347 When deploying Living Apps on a Bonita stack over HTTPS, SSL certificate validation may be disabled using the `--disable-certificate-check` option as follows:
  ```bash
  bcd -s <scenario> livingapp deploy -p <application_path> -c <configuration_path> --disable-certificate-check
  ```
  This option may be used when a **self-signed certificate** is installed on the target Bonita stack.
* BCD-377 Generate stack deployment information as a JSON file with status command  
  With `stack deploy` and `stack status` commands, Bonita stack information (including stack URL) can be generated as a JSON file by setting a file path into the `bcd_stack_json` variable. Refer to the [Scenario reference documentation](https://documentation.bonitasoft.com/bcd/${varVersion}/scenarios#toc0) for further information.

### Bugfixes

* BCD-370 Living Apps build fails when version range is used in Maven projects
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
* BCD-378 AWS EC2 inventory is not accurate when `bcd_stack_id` is overridden with extra args
