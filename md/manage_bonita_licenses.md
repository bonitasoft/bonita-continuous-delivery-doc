# How to manage Bonita licenses

This tutorial describes how to manage Bonita licenses from the command line using BCD.  
This feature lets you generate licenses programmatically for your Bonita Studio or Bonita Server.

## Prerequisites

To use this feature, you need to prepare a [scenario file](scenarios) with the following required information:

* The `bonita_version` variable
* [All licensing variables](https://documentation.bonitasoft.com/bcd/${varVersion}/scenarios#toc4)

::: info
An example scenario is provided with the **scenarios/manage_licenses.yml.EXAMPLE** file with required variables to manage
Bonita licenses with BCD.
:::

## Generating a new license

You can generate a new license providing a **Request Key**. Read more about Bonita licenses and how to generate a request key on this [documentation page](https://documentation.bonitasoft.com/bonita/${bonitaDocVersion}/licenses#toc2).

::: info
The request key has to be generated on the host where your Bonita Studio or Bonita Server will be running.
:::

Assuming your request key is `(CIVpYDRB8bhouXdWadLY1M9TVctwYUYu)`, here is an example usage of the `bcd license generate` command:

```bash
$ bcd -s scenarios/euwest1_performance.yml -y license generate -k '(CIVpYDRB8bhouXdWadLY1M9TVctwYUYu)' -o /path/to/output_directory
```

::: warning
The output directory (`/path/to/output_directory` in the above example) is related to your BCD Controller Docker container.  
In order to persist the generated licenses after exiting the BCD Controller, you may use a bind-mounted output directory. Still, you may copy your generated licenses while the BCD controller is running.
:::

## Revoking a license

You can revoke a Bonita license providing the path to a license file.

::: info
Revoking a license will make your license inactive and it will delete the license file you have provided.  
This operation releases a _slot_ to generate a new license as authorized by your Bonita subscription.
:::

Here is an example usage of the `bcd license revoke` command:

```bash
$ bcd -s scenarios/euwest1_performance.yml -y license revoke -p /path/to/bonita_license_file.lic
```
