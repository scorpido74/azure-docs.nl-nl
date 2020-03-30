---
title: Beheerde identiteiten gebruiken in Azure API Management | Microsoft Documenten
description: Meer informatie over het gebruik van beheerde identiteiten in API-beheer
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249631"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Beheerde identiteiten gebruiken in Azure API Management

In dit artikel ziet u hoe u een beheerde identiteit maakt voor een API Management-serviceinstantie en hoe u toegang krijgt tot andere bronnen. Met een beheerde identiteit die wordt gegenereerd door Azure Active Directory (Azure AD), kan uw API-beheerexemplaar eenvoudig en veilig toegang krijgen tot andere door Azure AD beveiligde bronnen, zoals Azure Key Vault. Deze identiteit wordt beheerd door Azure en vereist niet dat u geheimen indient of roteert. Zie [Wat wordt beheerd identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Een beheerde identiteit maken voor een API-beheerexemplaar

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API-beheerexemplaar als normaal en schakelt u de functie vervolgens in.

1. Maak een API-beheerinstantie in de portal zoals u dat normaal zou doen. Navigeer ernaar in de portal.
2. Selecteer **Beheerde serviceidentiteiten**.
3. Schakel Register over met Azure Active Directory naar Aan. Klik op Opslaan.

![MSI inschakelen](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>De sjabloon Azure Resource Manager gebruiken

U een API-beheerinstantie met een identiteit maken door de volgende eigenschap op te nemen in de resourcedefinitie:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Dit vertelt Azure om de identiteit voor uw API Management-exemplaar te maken en te beheren.

Een complete Azure Resource Manager-sjabloon kan er bijvoorbeeld als volgt uitzien:

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
> Momenteel kunnen beheerde identiteiten worden gebruikt om certificaten te verkrijgen van Azure Key Vault voor aangepaste domeinnamen van API Management. Meer scenario's zullen binnenkort worden ondersteund.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Een certificaat verkrijgen bij Azure Key Vault

#### <a name="prerequisites"></a>Vereisten
1. De Key Vault met het pfx-certificaat moet zich in hetzelfde Azure-abonnement bevinden en dezelfde Resourcegroep als de API-beheerservice. Dit is een vereiste van de sjabloon Azure Resource Manager.
2. Het inhoudstype van het geheim moet *toepassing/x-pkcs12*zijn. U het volgende script gebruiken om het certificaat te uploaden:

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
> Als de objectversie van het certificaat niet wordt geleverd, krijgt API Management automatisch de nieuwere versie van het certificaat nadat het is geüpload naar Key Vault.

In het volgende voorbeeld wordt een Azure Resource Manager-sjabloon weergegeven die de volgende stappen bevat:

1. Maak een API-beheerexemplaar met een beheerde identiteit.
2. Werk het toegangsbeleid van een Azure Key Vault-exemplaar bij en laat de instantie API-beheer er geheimen van verkrijgen.
3. Werk de instantie API-beheer bij door een aangepaste domeinnaam in te stellen via een certificaat van de instantie Key Vault.

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

Meer informatie over beheerde identiteiten voor Azure-bronnen:

* [Wat is beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates)
* [Verifiëren met een beheerde identiteit in een beleid](./api-management-authentication-policies.md#ManagedIdentity)
