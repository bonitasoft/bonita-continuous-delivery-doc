# Release notes

## Breaking changes

The following changes introduce incompatibility with BCD 2.x:

* By default, BCD no longer retrieves its dependenices from the fylesystem but uses a private docker registry. So the following parameters should be added to your scenarios :
```
bcd_registry_user: YOUR_USER_TO_BCD_DOCKER_REGISTRY
bcd_registry_password: YOUR_PASS_TO_BCD_DOCKER_REGISTRY
```

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.0.0 (2018-12-)

### New features

* Manage BCD dependencies through a private docker registry
* BCD sub commands to [manage Living Apps Configuration](livingapp_manage_configuration.md) (`bcd livingapp [extract-conf,merge-conf]`)
* Add Azure support to manage bcd stacks

### Enhancements

*

### Technology updates

*

### Bugfixes

* 
