# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 2.1.0 (2018-10-04)

### Bugfixes

* BCD-283 aws-google-auth : Captcha Required. Manually Login to remove this.

### Enhancements

* Generate stack deployment information as a JSON file in order to link bonita_url within Jenkins.
* Add a [Jenkins shared library](https://github.com/bonitasoft/bonita-jenkins-library) to have a `bcd` step directly in the Jenkins Pipeline DSL. 
