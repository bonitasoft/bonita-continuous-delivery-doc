# Release notes

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```

## What's new in 3.1.0 (2019-04-16)

### New features

* Configurable HTTP timeout for Living Apps deployment (default to 120 seconds)

### Bugfixes

* BCD-340 DS min and max pool sizes are not configured

### Enhancements

* BCD controller is now using alpine 3.9
