# How to configure Microsoft Azure for BCD

This tutorial describes how to configure Microsoft Azure cloud computing service for BCD.  
These configuration steps are required to allow BCD to programmatically manage your Azure resources and to be able to connect to your Bonita stack.

## Sign Up for Azure

To start using Azure services, you need to have a Microsoft account. If you do not have a Microsoft account yet, first sign up as described in this user guide: [Sign Up for a Microsoft account](https://support.microsoft.com/en-us/help/4026324/microsoft-account-sign-up-for-a-microsoft-account).  
Then go to the [Microsoft Azure](https://azure.microsoft.com/) website to sign up for Azure.


## Azure Setup for BCD

In order to use Azure resources, some configuration steps need to be performed as a prerequisite.  
The following steps are the basic requirements to set up your Azure account for BCD.

1. [Create a resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups).  
   A resource group may be shared by multiple BCD stacks. This allows to share virtual networks and network security groups among BCD stacks.  
   Choose a location (eg. `North Europe`) and give the resource group a name like: `bcd-northEurope-rg`.

1. [Create a virtual network](https://docs.microsoft.com/en-us/azure/virtual-network/quick-create-portal) on the same location and attach it to the resource group created previously.  
   Give the virtual network a name like: `bcd-northEurope-vnet`  
   Define a subnet for this virtual network. You may leave the default name `default`.

1. [Create a network security group](https://docs.microsoft.com/en-us/azure/virtual-network/manage-network-security-group) on the same location and attach it to the resource group created previously. Associate the security group with the subnet created previously.  
   Give the virtual network a name like: `bcd-northEurope-netsg`.
   - Add an Inbound rule to allow BCD to connect via SSH
     - **Source**: _Your IP_, **Destination port ranges**: 22, **Protocol**: Any
   - Add an Inbound rule to allow remote connection to Bonita Tomcat via HTTP
     - **Source**: _Your IP_, **Destination port ranges**: 8081, **Protocol**: TCP
   - Add an Inbound rule to allow remote connection to Bonita databases
     - **Source**: _Your IP_, **Protocol**: TCP
       - with `bonita_db_vendor: postgres` - **Destination port ranges**: 5432
       - with `bonita_db_vendor: mysql` - **Destination port ranges**: 3306
       - with `bonita_db_vendor: oracle` - **Destination port ranges**: 1521

1. [Create a custom Role using Azure CLI](https://docs.microsoft.com/en-us/azure/role-based-access-control/tutorial-custom-role-cli) (or Azure PowerShell). Both Azure CLI and PowerShell are available through [Azure Cloud Shell](https://azure.microsoft.com/en-us/features/cloud-shell/) directly from your browser.  
   Use the following JSON role definition in a `BCDReadWriteRole.json` file:
   ```json
   {
       "Actions": [
           "Microsoft.Resources/subscriptions/resourcegroups/read",
           "Microsoft.Network/register/action",
           "Microsoft.Network/virtualNetworks/read",
           "Microsoft.Network/virtualNetworks/subnets/read",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/networkInterfaces/read",
           "Microsoft.Network/networkInterfaces/write",
           "Microsoft.Network/networkInterfaces/delete",
           "Microsoft.Network/networkInterfaces/join/action",
           "Microsoft.Network/publicIPAddresses/read",
           "Microsoft.Network/publicIPAddresses/write",
           "Microsoft.Network/publicIPAddresses/delete",
           "Microsoft.Network/publicIPAddresses/join/action",
           "Microsoft.Compute/virtualMachines/read",
           "Microsoft.Compute/virtualMachines/write",
           "Microsoft.Compute/virtualMachines/delete",
           "Microsoft.Compute/disks/read",
           "Microsoft.Compute/disks/delete"
       ],
       "AssignableScopes": [
           "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
       ],
       "Description": "Read and write operations for BCD.",
       "Name": "BCD Read/Write"
   }
   ```
   **Note**: replace `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` with your actual subscription identifier. You may get your subscription identifier using Azure CLI with this command:  
   `az account list --output table`  
   Create your custom role using Azure CLI with this command:  
   `az role definition create --role-definition @BCDReadWriteRole.json`

1. [Create a Service Principal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal) to generate credentials (keys) for your BCD users. One Service Principal may manage keys for multiple users.  
   Register an application for BCD (**New application registration**):
   - **Name**: bcd
   - **Application type**: Web app / API
   - **Sign-on URL**: _Any valid URI, for instance_: https://mycomp.any
   
   Then go on with **Settings > Keys** to create keys (passwords) for your BCD users.

1. [Grant the Service Principal access to the custom role using Azure Portal](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal#grant-access). Other methods may be used to grant access.  
   - Open the **Access control (IAM)** page for your resource group
   - Assign to the **bcd** application the **BCD Read/Write** role (**Add permissions** page)

Finally, BCD is compatible with official [Ubuntu Server virtual machine images](https://azuremarketplace.microsoft.com/en/marketplace/apps/Canonical.UbuntuServer?tab=Overview) from Canonical.  
Check the [Requirements and compatibility](requirements-and-compatibility) page for the supported operating system versions.


## Azure Authentication for BCD

Azure credentials must be set as environment variables in your BCD controller in order to create and delete Azure resources with BCD. Here are the environment variables that need to be set:
- AZURE_SUBSCRIPTION_ID
- AZURE_CLIENT_ID
- AZURE_SECRET
- AZURE_TENANT
- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

In fact, there are 4 different information that you need to gather:
- <u>Subscription ID</u>: obtained from Portal's **Subscriptions** page as the **Subscription ID** field. It can also be obtained with Azure CLI command: `az account list --output table`
- <u>Client ID</u>: obtained from Portal's **Registered app** page as the **Application ID** field. It can also be obtained with Azure CLI command: `az ad app list | jq '.[] | {"displayName": .displayName, "appId": .appId}'` (provided [JQ](https://stedolan.github.io/jq/) is available)
- <u>Client Secret</u>: displayed once while creating keys (passwords) for users on the _bcd_ service principal.
- <u>Tenant ID</u>: obtained from Portal's **Azure Active Directory > Properties** page as the **Directory ID** field. It can also be obtained with Azure CLI command: `az account list`

Therefore you may use an environment script in order to set these environment variables in a more convenient way. Create a `bcd-azure.env` accessible from your BCD controller with the following content:
```bash
set -a

#
# Set values for the next 4 variables
#
AZURE_SUBSCRIPTION_ID=<YOUR_SUBSCRIPTION_ID>
AZURE_CLIENT_ID=<YOUR_CLIENT_ID>
AZURE_SECRET=<YOUR_CLIENT_SECRET>
AZURE_TENANT=<YOUR_TENANT_ID>

#
# DO NOT EDIT the following lines
#
ARM_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
ARM_CLIENT_ID=${AZURE_CLIENT_ID}
ARM_CLIENT_SECRET=${AZURE_SECRET}
ARM_TENANT_ID=${AZURE_TENANT}

set +a
```

Here is a sample `bcd-azure.env` file with dummy credentials:
```bash
set -a

AZURE_SUBSCRIPTION_ID=12345abc-123a-456b-789c-123456abcdef
AZURE_CLIENT_ID=54321abc-321a-654b-987c-654321abcdef
AZURE_SECRET=abcdefghiklmnopqrstuvwxyz123456789ABCDEFGHIJ
AZURE_TENANT=98765cba-987c-321d-012e-987654fedcba

ARM_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
ARM_CLIENT_ID=${AZURE_CLIENT_ID}
ARM_CLIENT_SECRET=${AZURE_SECRET}
ARM_TENANT_ID=${AZURE_TENANT}

set +a
```

Once your `bcd-azure.env` file is ready, source the file to set environment variables in your BCD controller as follows: `source bcd-azure.env`  
BCD is now ready to create/delete Azure resources as defined in your scenario using Azure credentials set in your environment.
