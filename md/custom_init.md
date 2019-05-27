# How to customize the Bonita container using initialization scripts

The Bonita Docker container deployed by BCD can be further customized thanks to a custom initialization mechanism.

This feature relies on the ability to extend the [Bonita Docker image](https://hub.docker.com/_/bonita/) through custom `*.sh` shell scripts.

## Where should I put custom initialization scripts?

Custom initialization files can be **static** or they can be **dynamic** while relying on runtime variables. Dynamic initialization files then leverage the [Jinja2 templating framework used by Ansible](http://docs.ansible.com/ansible/latest/playbooks_templating.html).

### Static initialization files

Static custom initialization files must be placed in BCD's `roles/bonita/files/custom-init.d` folder. All files from this folder will be uploaded to the target host regardless their extension. However only the files with a `.sh` extension will be executed.

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

### Dynamic initialization files

Dynamic custom initialization files must be placed in BCD's `roles/bonita/templates/custom-init.d` folder. All files with a `.j2` extension will be uploaded to the target host and stripped from the `.j2` suffix. However only the files with a `.sh.j2` extension will be executed.

Here's the directory layout for the provided `config-cluster.sh.j2` initialization script:
```
roles
├── bonita
│   └── templates
│       └── custom-init.d
│           ├── bonita-platform-sp-cluster-custom.properties.j2
│           ├── config-cluster.sh.j2
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

### 1. config-workers.sh

The `config-workers.sh` script is provided as part of BCD's core scripts.  
In particular it shows how to further configure the server using [Bonita Platform setup tool](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/BonitaBPM_platform_setup).

### 2. config-cluster.sh.j2

The `config-cluster.sh.j2` script template is provided as part of BCD's core scripts.  
In particular it shows how to conditionally configure a cluster deployment on AWS using BCD variables and [Jinja2 templating engine](http://docs.ansible.com/ansible/latest/playbooks_templating.html).

### 3. register-event-handler.sh

This sample script deploys and registers a Bonita engine Event handler as described in [Event handlers Documentation](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/event-handlers).

Assuming the following files:

*   `roles/bonita/files/custom-init.d/event-handler-example-1.0.0-SNAPSHOT.jar` (event handler JAR file)
*   `roles/bonita/files/custom-init.d/bonita-tenant-sp-custom.xml` (tenant configuration file where the event handler is registered)

Here's a sample `register-event-handler.sh` script:

```bash
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}
BONITA_SETUP_SH="${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-*omcat*/setup/setup.sh"

EVENT_HANDLER_FILENAME=event-handler-example-1.0.0-SNAPSHOT.jar


war_path=$(find "${BONITA_PATH}/Bonita"*"Subscription-${BONITA_VERSION}-"*"omcat"*"/server/webapps" -name bonita.war)
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
cp /opt/custom-init.d/bonita-tenant-sp-custom.xml ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-*omcat*/setup/platform_conf/current/tenant_template_engine/
${BONITA_SETUP_SH} push

# Create indicator file
touch ${indicator_path}
```

### 4. deploy-probe.sh

This sample script deploys [PSI Probe](https://github.com/psi-probe/psi-probe/wiki) (an _Advanced manager and monitor for Apache Tomcat_) along with Bonita in the Tomcat bundle.

In particular, it shows how to reference BCD variables in custom initialization files. All custom referenced variables can be defined at [BCD scenario](scenarios.md) level.

With this example, PSI Probe will be available at this URL: `http://<bonita_host>:8081/probe`. To connect to PSI Probe, use the credentials defined with the `custom_manager_username` and `custom_manager_password` variables. By default: `custom_manager_username=admin` and `custom_manager_password=t0psecret`.

#### deploy-probe.sh.j2

```bash
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi


BONITA_PATH=${BONITA_PATH:-/opt/bonita}
script_dir="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

pushd ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-*omcat*

# Allow Tomcat Manager from different host
cp ${script_dir}/manager-context.xml server/conf/Catalina/localhost/manager.xml

# PSI Probe
curl -sSL -o server/webapps/probe.war https://github.com/psi-probe/psi-probe/releases/download/{{ custom_psi_probe_version | default('3.0.0.RC2') }}/probe.war
cp ${script_dir}/tomcat-users.xml server/conf/tomcat-users.xml

# Create indicator file
touch ${indicator_path}
```


#### manager-context.xml.j2

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context antiResourceLocking="false" privileged="true" >
  <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="{{ custom_manager_allow_pattern | default('^.*$') }}" />
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```


#### tomcat-users.xml.j2

```xml
<?xml version="1.0" encoding="UTF-8"?>
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">

  <role rolename="probeuser" />
  <role rolename="poweruser" />
  <role rolename="poweruserplus" />
  
  <role rolename="manager-gui" />

  <user username="{{ custom_manager_username | default('admin') }}" password="{{ custom_manager_password | default('t0psecret') }}" roles="manager-gui" />

</tomcat-users>
```

### 5. More examples about REST API authorization

See [how to configure REST API authorization](how_to_configure_rest_api_authorization.md).
