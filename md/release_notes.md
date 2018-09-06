# Release notes

## Breaking changes

The following changes introduce incompatibility with BCD 1.0:

* For security reasons, pushing AWS user credentials to EC2 instances (as done with BCD 1.0) for cluster autodiscovery is no longer supported. It is replaced by an [IAM role](aws_prerequisites.md) assignment on EC2 instances. See `ec2_discovery_iam_role` in the [Scenarios documentation](scenarios.md).
* `bonita_http_api` variable is now set to `false` by default.
* For a better understanding, the `on-premises` value for `bcd_provider` variable has been replaced with `static_inventory`. You may have to update your [BCD scenarios](scenarios.md) accordingly.
* Bonita Docker images have been moved from `roles/bonita/files/docker` directory to `dependencies` directory.
* BCD commands for Provisioning are invoked with `bcd stack` parent command.

## Limitations and known issues

* The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.
* Due to [Ansible issue #35255](https://github.com/ansible/ansible/issues/35255) the warning message "could not match supplied host pattern" is displayed when there is no load balancer required for a noncluster deployment:
  ```
  [WARNING]: Could not match supplied host pattern, ignoring: load_balancer
  ```
* Single sign-on to AWS using G Suite may be broken due to this error with `aws-google-auth`:
  ```
  Captcha Required. Manually Login to remove this.
  ```
  This happens when the IP is flagged by Google. Related issue and pending pull request:
    - [aws-google-auth Issue #83](https://github.com/cevoaustralia/aws-google-auth/issues/83)
    - [aws-google-auth Pull request #105](https://github.com/cevoaustralia/aws-google-auth/pull/105)

## What's new in 2.0.2 (2018-09-06)

### Bugfixes

* BCD-280 Tomcat maxThreads configuration through 'bonita_max_threads' variable does not work.
* BCD-282 When overriding bonita_java_opts in a scenario, the Hazelcast cluster nodes discovery fails on AWS.

### Enhancements

* Clean the temporary directory used while deploying the LivingApp archive (`bcd livingapp deploy`).

## What's new in 2.0.1 (2018-08-02)

### Bugfixes

* BCD-274 Security fix: Disable Docker Remote API.

### Enhancements

* BCD-242 Fix dependencies version of license generation.
* BCD-270 Support Enterprise Edition Subscription for license generation.

## What's new in 2.0.0 (2018-06-07)

### New features

* BCD commands for [Living Application Management](_manage_living_application) (`bcd livingapp`)
* Add [Google G Suite Single Sign-On](aws_sso.md) (SSO) support
* BCD command to show BCD environment version (`bcd version`)
* [Jenkins standalone example](jenkins_example.md) with sample BCD pipeline
* Scenario [encryption capability](how_to_use_bcd_with_data_encrypted.md)

### Enhancements

* Handle `REST_API_DYN_AUTH_CHECKS` environment variable of Bonita Docker image
* Deactivate by default [Bonita HTTP API](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc9)
* Ports published by the Bonita stack are configurable

### Technology updates

* Upgrade BCD controller with Terraform v0.11.3
* Upgrade BCD controller with Ansible 2.5.0
* Support Provisioning of Ubuntu 16.04 hosts in addition to Ubuntu 14.04

### Bugfixes

* BCD-199 Stack undeploy command fails when Docker is not yet installed on target hosts
