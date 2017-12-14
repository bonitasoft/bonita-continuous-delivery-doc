# Bonita Container Custom Initialization

The Bonita Docker container deployed by BCD can be further customized thanks to a custom initialization mechanism.

This feature relies on the ability to extend the [Bonita Docker image](https://hub.docker.com/_/bonita/) through custom `*.sh` shell scripts.

## Where should I put custom initialization scripts?

Custom initialization scripts must be placed in BCD's `roles/bonita/files/custom-init.d` folder. Each script to execute must have a `.sh` extension.

Here's the directory layout for a `register-event-handler.sh` custom script:
```
roles
├── bonita
│   ├── files
│   │   ├── custom-init.d
│   │   │   ├── bonita-tenant-sp-custom.xml
│   │   │   ├── config-workers.sh
│   │   │   ├── event-handler-example-1.0.0-SNAPSHOT.jar
│   │   │   └── register-event-handler.sh
```

## When are custom initialization scripts invoked?

Custom initialization scripts are invoked once the database has been initialized and the Tomcat server has been configured with [Bonita Platform setup tool](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/BonitaBPM_platform_setup).

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
In particular it shows how to further configure the server using [Bonita Platform setup tool](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/BonitaBPM_platform_setup).

### register-event-handler.sh

This sample script deploys and registers a Bonita engine Event handler as described in [Event handlers Documentation](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/event-handlers).

Assuming the following files:

- `roles/bonita/files/custom-init.d/event-handler-example-1.0.0-SNAPSHOT.jar` (event handler JAR file)
- `roles/bonita/files/custom-init.d/bonita-tenant-sp-custom.xml` (tenant configuration file where the event handler is registered)

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

### add-javamelody.sh

This sample script deploys the [JavaMelody monitoring tool](https://github.com/javamelody/javamelody/wiki) to Bonita web application. This example shows how to download external jars and include them to Bonita WAR.

With this example, JavaMelody will be available at this URL: `http://<bonita_host>:8081/bonita/monitoring`.

```bash
#!/bin/bash

set -euxo pipefail

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}


war_path=$(find "${BONITA_PATH}/Bonita"*"Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/server/webapps" -name bonita.war)
workdir="${BONITA_FILES}/add-javamelody"

rm -rf ${workdir} && mkdir -p ${workdir}

pushd ${workdir}

# download javamelody jars
mkdir -p WEB-INF/lib
pushd WEB-INF/lib
curl -sSLOJ https://github.com/javamelody/javamelody/releases/download/javamelody-core-1.70.0/javamelody-core-1.70.0.jar
curl -sSLOJ https://repo1.maven.org/maven2/org/jrobin/jrobin/1.5.9/jrobin-1.5.9.jar
popd

# repackage war
zip -r "${war_path}" WEB-INF/lib
```
