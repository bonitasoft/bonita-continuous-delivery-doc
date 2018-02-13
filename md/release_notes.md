# Release notes

## Limitations and known issues

- On a BCD controller we can manage only one Terraform stack at a time.


## What's new in 1.0.2 (2018-02-02)

### Bugfixes

- BCD-142 Failures while loading Docker images do not stop BCD command
- BCD-189 Database instance (AWS) and container (Docker) is created although 'bonita_db_host' is set
- BCD-190 boto file is required for Vagrant deployments
- BCD-191 'bcd status' command fails when docker is not installed yet on target hosts
- BCD-192 Timestamp is not displayed in BCD CLI stdout
- BCD-196 '--nocolor' is not a recognized BCD CLI option


## What's new in 1.0.1 (2018-01-05)

### Bugfixes

- BCD-104 An EC2 instance is created for database even when rds="true"
- BCD-141 Undeploy doesn't free disk space of docker volumes
