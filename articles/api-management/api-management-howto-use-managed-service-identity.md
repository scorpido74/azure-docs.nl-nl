---
title: Beheerde identiteiten gebruiken in azure API Management | Microsoft Docs
description: Meer informatie over het gebruik van beheerde identiteiten in API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249631"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Beheerde identiteiten gebruiken in azure API Management

In dit artikel wordt beschreven hoe u een beheerde identiteit voor een API Management service-exemplaar maakt en hoe u toegang krijgt tot andere bronnen. Met een beheerde identiteit die is gegenereerd door Azure Active Directory (Azure AD) kan uw API Management-exemplaar eenvoudig en veilig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault. Deze identiteit wordt beheerd door Azure en u hoeft geen geheimen in te richten of te draaien. Zie [Wat is beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Een beheerde identiteit voor een API Management-exemplaar maken

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API Management exemplaar als normaal en schakelt u de functie in.

1. Maak een API Management-exemplaar in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.
2. Selecteer **beheerde service-identiteiten**.
3. Schakel registratie bij Azure Active Directory in op aan. Klik op Opslaan.

![MSI inschakelen](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>De Azure Resource Manager-sjabloon gebruiken

U kunt een API Management-exemplaar maken met een identiteit door de volgende eigenschap op te nemen in de resource definitie:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Dit vertelt Azure het maken en beheren van de identiteit voor uw API Management-exemplaar.

Een complete Azure Resource Manager sjabloon kan er bijvoorbeeld als volgt uitzien:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>De beheerde service-identiteit gebruiken om toegang te krijgen tot andere bronnen

> [!NOTE]
> Op dit moment kunnen beheerde identiteiten worden gebruikt voor het verkrijgen van certificaten van Azure Key Vault voor API Management aangepaste domein namen. Binnenkort worden er meer scenario's ondersteund.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Een certificaat verkrijgen van Azure Key Vault

#### <a name="prerequisites"></a>Vereisten
1. De Key Vault die het pfx-certificaat bevat, moeten zich in hetzelfde Azure-abonnement en dezelfde resource groep bevallen als de API Management-service. Dit is een vereiste voor de Azure Resource Manager sjabloon.
2. Het inhouds type van het geheim moet *Application/x-pkcs12/pfx-profiel*zijn. U kunt het volgende script gebruiken om het certificaat te uploaden:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Als de object versie van het certificaat niet is gegeven, wordt door API Management automatisch de nieuwere versie van het certificaat opgehaald nadat het is geüpload naar Key Vault.

In het volgende voor beeld ziet u een Azure Resource Manager sjabloon die de volgende stappen bevat:

1. Maak een API Management-exemplaar met een beheerde identiteit.
2. Het toegangs beleid van een Azure Key Vault-exemplaar bijwerken en toestaan dat het API Management exemplaar geheimen van de instantie kan verkrijgen.
3. Werk het API Management-exemplaar bij door een aangepaste domein naam in te stellen via een certificaat van de Key Vault instantie.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beheerde identiteiten voor Azure-resources:

* [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates)
* [Verifiëren met een beheerde identiteit in een beleid](./api-management-authentication-policies.md#ManagedIdentity)
