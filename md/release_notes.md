# Release notes

## Breaking changes


## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```


## What's new in 3.4.0

### Technology upgrade
* Upgrade Python to v3
* Upgrade ansible to v2.2.9
* Upgrade terraform to 0.12.26
* New docker image OS to provide


### Bug fixes
* BCD-422: Authentication to AWS using G Suite SSO fails in BCD controller
* BCD-458: Livingapp deploy fails when tenant password contains '$' (dollar sign) character
