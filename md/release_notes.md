# Release notes

## Limitations and known issues

* On a BCD controller we can manage only one Terraform stack at a time.
* Due to [ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
```
[WARNING]: Could not match supplied host pattern, ignoring: load_balancer
```

## What's new in 2.0.0

### Enhancements

* Handle REST_API_DYN_AUTH_CHECKS environment variable of Bonita docker image
* Deactivate by default the Bonita [HTTP API](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc9)
* Add Google G Suite SSO support

### Bugfixes
