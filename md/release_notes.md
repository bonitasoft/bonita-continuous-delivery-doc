# Release notes

## Breaking changes

The following changes introduce incompatibility with BCD 2.x:

* By default, BCD no longer retrieves its dependencies from the filesystem but uses a secured public Docker registry. So the following variables must be added to your scenarios:
  ```
  bcd_registry_user: YOUR_USER_TO_BCD_DOCKER_REGISTRY
  bcd_registry_password: YOUR_PASS_TO_BCD_DOCKER_REGISTRY
  ```
  As a BCD customer, contact your sales representative to get your access to the secured registry.

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.0.0 (2018-12-06)

### New features

* Manage Docker dependencies through a secured public registry
* [Manage Bonita licenses](manage_bonita_licenses.md) with BCD sub-commands (`bcd license [generate,revoke]`)
* [Manage Living Apps Configuration](livingapp_manage_configuration.md) with BCD sub-commands (`bcd livingapp [extract-conf,merge-conf]`) for Bonita from 7.8.0
* Create and delete Microsoft Azure resources for the Bonita stack automatically (`bcd_provider: azure`)

### Enhancements

* Enabling endpoint heuristics to guess endpoints for AWS regions that aren’t integrated yet into [boto](http://docs.pythonboto.org). For example, BCD can now manage the AWS region eu-west-3 (Paris) despite the issue [#3783](https://github.com/boto/boto/issues/3783) is still opened.
* The `bonita_edition` variable is no longer used and it will be ignored if it is still defined in scenarios. The Bonita edition is automatically inferred from your subscription.
