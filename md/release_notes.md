# Release notes

## Breaking changes
* Controller image is now `Debian` based (and not Alpine Linux anymore). Any custom controller images will require migration and rebuild.

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
* New docker image OS to provide easier customization and access to the maven cache


### Bug fixes
* BCD-422: Authentication to AWS using G Suite SSO fails in BCD controller
* BCD-458: Livingapp deploy fails when tenant password contains '$' (dollar sign) character

::: warning

The use of `latest` (or no tag) tag on `BCD Controller` image is **deprecated** !
This tag will be removed from the https://quay.io registry in the next release.

By specifying a BCD Controller version in your configuration, you ensure consistency with the configuration files that comes
with the zip distribution.

From now (and for previous versions), make sure to always specify the BCD version when using `BCD Controller` docker image.
:::