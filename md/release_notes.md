# Release notes

## Limitations and known issues

- The same BCD stack cannot be managed with multiple BCD controller instances due to the use of Terraform "local" backend.

## What's new in 1.0.5 (2018-05-04)

### Bugfixes

- BCD-200 Database container is not created with provided Vagrant inventories
- BCD-241 'Install license modules' task fails
- BCD-243 vagrant failed to download ubuntu box: 404 Not Found


## What's new in 1.0.4 (2018-04-05)

### Bugfixes

- BCD-234 'Install python modules' task fails due to pip 10


## What's new in 1.0.3 (2018-03-08)

### Bugfixes

- BCD-204 Hibernate cache is not disabled on cluster deployments

### Internal refactoring

- BCD-171 Refactor license request script using bonita_license module
- Set bcd_cli module version to 1.0.2


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
