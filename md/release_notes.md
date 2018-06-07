# Release notes

## Breaking changes

* For security reasons, pushing AWS user credentials to EC2 instances (as done with BCD 1.0.x) for cluster autodiscovery is no longer supported and it's replaced by an [IAM role](aws_prerequisites.md) assignement on EC2 instances. See `ec2_discovery_iam_role` into [scenarios documentation](scenarios.md).
* `bonita_http_api` value is now set to `false` by default.
* For a better comprehension the value of bcd_provider `on-premises` has been replaced by `static_inventory`, you may have to update your [scenarios](scenarios.md) accordingly.
* Bonita Docker images have been moved from `roles/bonita/files/docker/` to `dependencies` directories

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
```
[WARNING]: Could not match supplied host pattern, ignoring: load_balancer
```

## What's new in 2.0.0 (2018-06-07)

* This version is compatible with Bonita 7.7.0

### Enhancements

* Handle REST_API_DYN_AUTH_CHECKS environment variable of Bonita docker image
* Deactivate by default the Bonita [HTTP API](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc9)
* Add Google G Suite SSO support

### Bugfixes
* BCD-199	bcd undeploy command fails when docker is not yet installed on target hosts
