# Release notes

## Breaking changes

The following changes introduce incompatibility with BCD 2.x:

* By default, BCD no longer retrieves its dependencies from the fylesystem but uses a private docker registry. So the following parameters should be added to your scenarios:
```
bcd_registry_user: YOUR_USER_TO_BCD_DOCKER_REGISTRY
bcd_registry_password: YOUR_PASS_TO_BCD_DOCKER_REGISTRY
```
As a BCD customer, contact your sales representative to get your access to the private registry.

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.0.0 (2018-12-)

### New features

* Manage Docker dependencies through a private registry
* Manage [Living Apps Configuration](livingapp_manage_configuration.md) with BCD sub-commands (`bcd livingapp [extract-conf,merge-conf]`)
* Create and delete Microsoft Azure resources for the Bonita stack automatically (`bcd_provider: azure`)

### Enhancements

* Enabling endpoint heuristics to guess endpoints for AWS regions that aren’t built in yet into [boto](http://docs.pythonboto.org). For example, BCD can now manage the AWS region eu-west-3 (Paris) despite the issue [#3783](https://github.com/boto/boto/issues/3783) is still opened.

### Technology updates

*

### Bugfixes

* 
