---
title: Toegangsreferenties veilig opslaan
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over het veilig opslaan van toegangsreferenties op de Data Science Virtual Machine. U leert hoe u beheerde serviceidentiteiten en Azure Key Vault gebruikt om toegangsreferenties op te slaan.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science proces
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 1cb0c5094d49eac5a1c8f63406a28d2927d8fa94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477320"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Toegangsreferenties veilig opslaan op een Azure Data Science Virtual Machine

Het is gebruikelijk dat de code in cloudtoepassingen referenties bevat voor het authenticeren naar cloudservices. Het beheren en beveiligen van deze referenties is een bekende uitdaging bij het bouwen van cloudtoepassingen. In het ideale plaats worden referenties nooit weergegeven op werkstations van ontwikkelaars of mogen ze worden ingecheckt bij bronbeheer.

De [beheerde identiteiten voor Azure-bronnen](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) maken het oplossen van dit probleem eenvoudiger door Azure-services een automatisch beheerde identiteit te geven in Azure Active Directory (Azure AD). U kunt deze identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, zonder dat u referenties in uw code hoeft te hebben.

Een manier om referenties te beveiligen is door Windows Installer (MSI) te gebruiken in combinatie met [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), een beheerde Azure-service om geheimen en cryptografische sleutels veilig op te slaan. U toegang krijgen tot een sleutelkluis met behulp van de beheerde identiteit en vervolgens de geautoriseerde geheimen en cryptografische sleutels ophalen uit de sleutelkluis.

De documentatie over beheerde identiteiten voor Azure-bronnen en Key Vault bevat een uitgebreide bron voor uitgebreide informatie over deze services. De rest van dit artikel loopt door het basisgebruik van MSI en Key Vault op de Data Science Virtual Machine (DSVM) om toegang te krijgen tot Azure-bronnen. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Een beheerde identiteit maken op de DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Toegangsmachtigingen voor Key Vault toewijzen aan een VM-principal

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Toegang tot een geheim in de sleutelkluis van de DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Toegang tot opslagsleutels van de DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Toegang tot de sleutelkluis van Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Toegang tot de sleutelkluis vanuit Azure CLI

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
