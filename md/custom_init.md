# Bonita Container Custom Initialization

The Bonita Docker container deployed by BCD can be further customized thanks to a custom initialization mechanism.

This feature relies on the ability to extend the [Bonita Docker image](https://hub.docker.com/_/bonita/) through custom `*.sh` shell scripts.

## Where should I put custom initialization scripts?

Custom initialization scripts must be placed in BCD's `roles/bonita/files/custom-init.d` folder. Each script to execute must have a `.sh` extension.

Here's the directory layout for a `replace-logo.sh` custom script:
```
roles
├── bonita
│   ├── files
│   │   ├── custom-init.d
│   │   │   ├── config-workers.sh
│   │   │   ├── login-logo.png
│   │   │   ├── logo.png
│   │   │   └── replace-logo.sh
```

## When are custom initialization scripts invoked?

Custom initialization scripts are invoked once the database has been initialized and the Tomcat server has been configured with [Bonita Platform setup tool](https://documentation.bonitasoft.com/?page=BonitaBPM_platform_setup).

Hence the Bonita Docker image startup sequence can be described as follows:

1. Initialize database: `setup.sh init`
1. Configure Tomcat server: `setup.sh configure`
1. Execute custom initialization `*.sh` scripts found in the container's `/opt/custom-init.d` folder
1. Start Tomcat server

::: info
**Warning**: all custom scripts are re-executed each time the Bonita Docker container is restarted.

If you want your script to be executed only once, you need to handle the conditional execution in your custom script itself.
For example:
```bash
#!/bin/bash

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

#
# Do some interesting stuff
# [...]

# Create indicator file
touch ${indicator_path}
```
:::

## In which order are custom initialization scripts invoked?

Custom scripts are executed in natural sort order as implemented by the `ls` shell command. It is also referred as _natural sort of (version) numbers within text_.

More precisely scripts are executed in the order returned by this command: `ls -v /opt/custom-init.d/*.sh`.

## Examples

### config-workers.sh

Ths `config-workers.sh` script is provided as part of BCD's core scripts.
In particular it shows how to further configure the server using [Bonita Platform setup tool](https://documentation.bonitasoft.com/?page=BonitaBPM_platform_setup).

### replace-logo.sh

This sample script replaces the default logo images (login and header logos) in Bonita Portal.

Assuming the following custom logo files:

- roles/bonita/files/custom-init.d/login-logo.png
- roles/bonita/files/custom-init.d/logo.png

Here's a sample `replace-logo.sh` script:

```bash
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}

war_path=$(find "${BONITA_PATH}/Bonita"*"Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/server/webapps" -name bonita.war)
script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
workdir="${BONITA_FILES}/replace-logo"
rm -rf ${workdir} && mkdir -p ${workdir}

pushd ${workdir}

# extract themes from war
mkdir -p WEB-INF/classes
unzip -j "${war_path}" "WEB-INF/classes/bonita-portal-theme*.zip" -d WEB-INF/classes

# prepare logo images
mkdir -p skin/images
cp ${script_dir}/login-logo.png ${script_dir}/logo.png skin/images/

# replace logo images in themes
ls -1 WEB-INF/classes/bonita-portal-theme*.zip | xargs -I {} -n1 zip -r {} skin

# repackage war
zip -r "${war_path}" "WEB-INF/classes/bonita-portal-theme*.zip"

touch ${indicator_path}
```

### activate-all-dynamic-checks.sh

This sample script activates all standard [REST API dynamic authorization rules](https://documentation.bonitasoft.com/?page=rest-api-authorization) to further secure Bonita REST API. These rules are meant to cover the most frequent cases.

```bash
#!/bin/bash

set -euxo pipefail

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_SETUP_SH=$(find "${BONITA_PATH}/Bonita"*"-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup" -name setup.sh)

# Pull current configuration
${BONITA_SETUP_SH} pull

# Uncomment all default rules
find ${BONITA_PATH}/Bonita*-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/platform_conf/current -name "dynamic-permissions-checks-custom.properties" | xargs -I{} -n10 \
  sed -i "s/^#\(GET|\|POST|\|PUT|\|DELETE|\)/\1/g"

# Update configuration
${BONITA_SETUP_SH} push
```

### register-event-handler.sh

This sample script deploys and registers a Bonita engine Event handler as described in [Event handlers Documentation](https://documentation.bonitasoft.com/?page=event-handlers).

Assuming the following files:

- roles/bonita/files/custom-init.d/event-handler-example-1.0.0-SNAPSHOT.jar (event handler JAR file)
- roles/bonita/files/custom-init.d/bonita-tenant-sp-custom.xml (tenant configuration file where the event handler is registered)

Here's a sample `register-event-handler.sh` script:


```bash
#!/bin/bash

set -euxo pipefail

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}
BONITA_SETUP_SH="${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/setup.sh"

EVENT_HANDLER_FILENAME=event-handler-example-1.0.0-SNAPSHOT.jar


war_path=$(find "${BONITA_PATH}/Bonita"*"Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/server/webapps" -name bonita.war)
script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
workdir="${BONITA_FILES}/register-event-handler"
rm -rf ${workdir} && mkdir -p ${workdir}

pushd ${workdir}

# copy event handler jar
mkdir -p WEB-INF/lib
cp ${script_dir}/${EVENT_HANDLER_FILENAME} WEB-INF/lib/

# repackage war
zip -r "${war_path}" "WEB-INF/lib/${EVENT_HANDLER_FILENAME}"


# register event handler
${BONITA_SETUP_SH} pull
cp /opt/custom-init.d/bonita-tenant-sp-custom.xml ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/platform_conf/current/tenant_template_engine/
${BONITA_SETUP_SH} push
```
