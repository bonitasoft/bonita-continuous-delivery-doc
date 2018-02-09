# Release notes

## Limitations and known issues

- On a BCD controller we can manage only one Terraform stack at a time.

## What's new in 1.0.1 (2018-01-05)

### Bugfixes

- BCD-104 A database instance is created on ec2 even when rds="true"
- BCD-141 Undeploy doesn't free disk space of docker volumes

## What's new in 1.0.2 (2018-02-02)

### Bugfixes

* BCD-142       Exit 1 in docker load do not stop ansible playbook
* BCD-189       Database instance (AWS) and container (Docker) is created although bonita_db_host is set
* BCD-190       boto file is required for vagrant deployments
* BCD-191       bcd status command fails when docker is not yet installed on target hosts
