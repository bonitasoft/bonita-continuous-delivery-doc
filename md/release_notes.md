# Release notes

## Limitations and known issues

- On a BCD controller we can manage only one Terraform stack at a time.

## What's new in 1.0.1 (2018-01-05)

### Bugfixes

- BCD-104 A database instance is created on ec2 even when rds="true"
- BCD-141 Undeploy doesn't free disk space of docker volumes
