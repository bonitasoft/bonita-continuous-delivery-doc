# How to configure REST API authorization

The Bonita container is launched with REST_API_DYN_AUTH_CHECKS flag set to true by default.
It means that all [dynamic permissions checks](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization#toc2) are activated.

In the sections below we will manipluate configuration into templates and not at a specific tenant. Indeed the default tenant (1) will be created during the first startup of bonita.

## Deactivating dynamic permissions checks

Since 2.0.x, BCD manages the REST_API_DYN_AUTH_CHECKS environment through the [scenario variable](scenarios.md) `bonita_rest_api_dyn_auth_checks`.
So to deactivate dynamic permissions checks you will just have to add the following line into your scenario
```
bonita_rest_api_dyn_auth_checks: false
```

## Adding custom permissions

As described in [Bonita documentation](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/rest-api-authorization?hash=debug#toc1), the custom-permissions-mapping.properties file contains custom rules that supplement the resource permissions and compound permissions. By default, this file is empty, because the compound permissions definitions automatically manage the permissions needed for default and custom profiles, and for default and custom pages.

If you want to override the default behavior, you can add rules to this file by adding this kind of script `roles/bonita/files/custom-init.d/add-custom-permissions.sh`

```
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}
BONITA_SETUP_SH="${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/setup.sh"

# define custom permissions
# the profile User have now the permission Organization management and Organization visualization
${BONITA_SETUP_SH} pull
echo -e "\nprofile|User=[organization_management, organization_visualization]" >> ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/platform_conf/current/tenant_template_portal/custom-permissions-mapping.properties
${BONITA_SETUP_SH} push

# Create indicator file
touch ${indicator_path}
```

For a more advanced configuration you can also provide directly a file `roles/bonita/files/custom-init.d/custom-permissions-mapping.properties` and push it with a script like `roles/bonita/files/custom-init.d/add-custom-permissions-file.sh`


```
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}
BONITA_SETUP_SH="${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/setup.sh"

# define custom permissions
${BONITA_SETUP_SH} pull
cp /opt/custom-init.d/custom-permissions-mapping.properties ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/platform_conf/current/tenant_template_portal/
${BONITA_SETUP_SH} push

# Create indicator file
touch ${indicator_path}
```


## Enabling debug mode

If [debug mode](https://documentation.bonitasoft.com/bonita/${varVersion}/rest-api-authorization?hash=debug) is activated, whenever you update a configuration file or a dynamic check script, the changes take effect immediately.

To activate debug mode you can create a script like `roles/bonita/files/custom-init.d/activate-debug-mode.sh`

```
#!/bin/bash

set -euxo pipefail

indicator_path=/opt/$(basename $BASH_ARGV)-executed
if [ -f ${indicator_path} ]; then
  echo "Custom script already executed" && return 0
fi

BONITA_PATH=${BONITA_PATH:-/opt/bonita}
BONITA_FILES=${BONITA_FILES:-/opt/files}
BONITA_SETUP_SH="${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/setup.sh"

# activate debug mode
${BONITA_SETUP_SH} pull
sed -i 's/security.rest.api.authorizations.check.debug.*/security.rest.api.authorizations.check.debug true/' ${BONITA_PATH}/Bonita*Subscription-${BONITA_VERSION}-Tomcat-${TOMCAT_VERSION}/setup/platform_conf/current/tenant_template_portal/security-config.properties
${BONITA_SETUP_SH} push

# Create indicator file
touch ${indicator_path}
```

