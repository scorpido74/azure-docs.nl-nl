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
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 028b26537c9fe8a976dbc68a4776b2ea4101d811
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789400"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Beheerde identiteiten gebruiken in azure API Management

In dit artikel wordt beschreven hoe u een beheerde identiteit voor een API Management exemplaar maakt en hoe u toegang krijgt tot andere bronnen. Met een beheerde identiteit die is gegenereerd door Azure Active Directory (Azure AD) kan uw API Management-exemplaar eenvoudig en veilig toegang krijgen tot andere met Azure AD beveiligde resources, zoals Azure Key Vault. Deze identiteit wordt beheerd door Azure en u hoeft geen geheimen in te richten of te draaien. Zie [Wat is beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

Aan een API Management-exemplaar kunnen twee typen identiteiten worden verleend:

- Een door het **systeem toegewezen identiteit** is gekoppeld aan uw service en wordt verwijderd als uw service wordt verwijderd. De service kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de **gebruiker toegewezen identiteit** is een zelfstandige Azure-resource die kan worden toegewezen aan uw service. De service kan meerdere door de gebruiker toegewezen identiteiten (*) hebben.

## <a name="create-a-system-assigned-managed-identity-for-an-api-management-instance"></a>Een door het systeem toegewezen beheerde identiteit maken voor een API Management-exemplaar

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API Management exemplaar als normaal en schakelt u de functie in.

1. Maak een API Management-exemplaar in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.
2. Selecteer **beheerde identiteiten**.
3. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Klik op **Opslaan**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Systeem toegewezen beheerde identiteit inschakelen." border="true":::


### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In de volgende stappen wordt uitgelegd hoe u een API Management exemplaar maakt en hoe u er een identiteit aan toewijst met behulp van Azure PowerShell. 

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/install-az-ps)en voert `Connect-AzAccount` u uit om een verbinding te maken met Azure.

2. Maak een API Management-exemplaar met behulp van Azure PowerShell. Zie voor meer voor beelden van het gebruik van Azure PowerShell met API Management-exemplaar [API Management Power shell](powershell-samples.md)-voor beelden:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Update en bestaande instantie voor het maken van de identiteit:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

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
        "apiVersion": "2019-01-01",
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

Wanneer het exemplaar wordt gemaakt, heeft het de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

De eigenschap tenantId identificeert de Azure AD-Tenant waartoe de identiteit behoort. De principalId is een unieke id voor de nieuwe identiteit van het exemplaar. De Service-Principal in azure AD heeft dezelfde naam die u hebt opgegeven voor uw API Management-exemplaar.


> [!NOTE]
> Een API Management-exemplaar kan tegelijkertijd zowel aan het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Scenario's die worden ondersteund

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Een aangepast TLS/SSL-certificaat voor het API Management-exemplaar verkrijgen van Azure Key Vault
De door het systeem toegewezen identiteit van een API Management service kan worden gebruikt om aangepaste TLS/SSL-certificaten op te halen die zijn opgeslagen in Azure Key Vault. Deze certificaten kunnen worden toegewezen aan aangepaste domeinen in API Management-exemplaar.

1. Het inhouds type van het geheim moet *Application/x-pkcs12/pfx-profiel*zijn.
2. Het geheim eind punt van het Key Vault certificaat moet worden gebruikt, dat het feitelijke geheim bevat.

> [!Important]
> Als de object versie van het certificaat niet is gegeven, wordt door API Management automatisch de nieuwere versie van het certificaat opgehaald nadat het is geüpload naar Key Vault binnen vier uur

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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
        "apiVersion": "2019-01-01",
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

#### <a name="authenticate-using-api-management-identity-to-the-backend"></a>Verifiëren met behulp van API Management identiteit voor de back-end

De toegewezen identiteit van het systeem kan worden gebruikt voor verificatie bij uw back-end met het beleid voor [beheerde identiteiten](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="create-a-user-assigned-managed-identity-for-an-api-management-instance"></a>Een door de gebruiker toegewezen beheerde identiteit maken voor een API Management-exemplaar

> [!NOTE]
> Een API Management-exemplaar kan worden gekoppeld aan Maxi maal 10 door de gebruiker toegewezen beheerde identiteit.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal wilt instellen, maakt u eerst een API Management exemplaar als normaal en schakelt u de functie in.

1. Maak een API Management-exemplaar in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.
2. Selecteer **beheerde identiteiten**.
3. Klik op het tabblad **toegewezen door gebruiker** op **toevoegen**.
4. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Door de gebruiker toegewezen beheerde identiteit inschakelen." border="true":::

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In de volgende stappen wordt uitgelegd hoe u een API Management exemplaar maakt en hoe u er een identiteit aan toewijst met behulp van Azure PowerShell. 

1. Als dat nodig is, installeert u de Azure PowerShell met behulp van de instructies in de [Azure PowerShell Guide](/powershell/azure/install-az-ps)en voert `Connect-AzAccount` u uit om een verbinding te maken met Azure.

2. Maak een API Management-exemplaar met behulp van Azure PowerShell. Zie voor meer voor beelden van het gebruik van Azure PowerShell met API Management-exemplaar [API Management Power shell](powershell-samples.md)-voor beelden:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Update en de bestaande service om een identiteit aan de service toe te wijzen.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="using-the-azure-resource-manager-template"></a>De Azure Resource Manager-sjabloon gebruiken

U kunt een API Management-exemplaar maken met een identiteit door de volgende eigenschap op te nemen in de resource definitie:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Als u het door de gebruiker toegewezen type toevoegt, krijgt Azure de door de gebruiker toegewezen identiteit te gebruiken die voor uw exemplaar is opgegeven.

Een complete Azure Resource Manager sjabloon kan er bijvoorbeeld als volgt uitzien:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
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
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Wanneer de service wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

De principalId is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De clientId is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit tijdens runtime-aanroepen moet worden gebruikt.

> [!NOTE]
> Een API Management-exemplaar kan tegelijkertijd zowel aan het systeem toegewezen als door de gebruiker toegewezen identiteiten hebben. In dit geval is de `type` eigenschap`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Scenario's die worden ondersteund

#### <a name="authenticate-using-user-assigned-identity-to-the-backend"></a>Verificatie met behulp van een door de gebruiker toegewezen identiteit aan de back-end

De door de gebruiker toegewezen identiteit kan worden gebruikt voor verificatie bij uw back-end met het beleid voor [beheerde identiteiten](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="remove-an-identity"></a><a name="remove"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te scha kelen met behulp van de portal of Azure Resource Manager sjabloon op dezelfde manier als waarop deze is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het identiteits type in op geen.

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit Azure AD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit Azure AD wanneer het API Management exemplaar wordt verwijderd.

Als u alle identiteiten wilt verwijderen met Azure Resource Manager sjabloon, moet u deze sectie bijwerken:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Als een API Management-exemplaar is geconfigureerd met een aangepast SSL-certificaat van de sleutel kluis en er wordt geprobeerd om de beheerde identiteit uit te scha kelen, mislukt de aanvraag.
> De klant kan deblokkeren door van Azure Key Vault-certificaat te scha kelen om een inline-gecodeerd certificaat te leveren en vervolgens beheerde identiteit uit te scha kelen. Raadpleeg [aangepast domein configureren](configure-custom-domain.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beheerde identiteiten voor Azure-resources:

* [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-quickstart-templates)
* [Verifiëren met een beheerde identiteit in een beleid](./api-management-authentication-policies.md#ManagedIdentity)
