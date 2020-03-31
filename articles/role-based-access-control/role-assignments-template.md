---
title: Roltoewijzingen toevoegen met RBAC- en Azure Resource Manager-sjablonen
description: Meer informatie over het verlenen van toegang tot Azure-bronnen voor gebruikers, groepen, serviceprincipals of beheerde identiteiten met Behulp van RBAC-sjablonen (Azure Resource Manager).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9f817880f938f5d03024e3aacd9b84817a5ac721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77138287"
---
# <a name="add-role-assignments-using-azure-rbac-and-azure-resource-manager-templates"></a>Roltoewijzingen toevoegen met Azure RBAC- en Azure Resource Manager-sjablonen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Naast het gebruik van Azure PowerShell of de Azure CLI u rollen toewijzen met [Azure Resource Manager-sjablonen.](../azure-resource-manager/templates/template-syntax.md) Sjablonen kunnen handig zijn als u resources consistent en herhaaldelijk moet implementeren. In dit artikel wordt beschreven hoe u rollen toewijzen met behulp van sjablonen.

## <a name="get-object-ids"></a>Object-geïdentificeerde objecten oppakken

Als u een rol wilt toewijzen, moet u de id opgeven van de gebruiker, groep of toepassing waaraan u de rol wilt toewijzen. De ID heeft `11111111-1111-1111-1111-111111111111`het formaat: . U de id opmaken via de Azure-portal, Azure PowerShell of Azure CLI.

### <a name="user"></a>Gebruiker

Als u de id van een gebruiker wilt krijgen, u de opdrachten [get-AzADUser](/powershell/module/az.resources/get-azaduser) of [az-advertentiegebruikers gebruiken.](/cli/azure/ad/user#az-ad-user-show)

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Groep

Als u de id van een groep wilt krijgen, u de opdrachten [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) of [az-advertentiegroep weergeven](/cli/azure/ad/group#az-ad-group-show) gebruiken.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="application"></a>Toepassing

Als u de id van een serviceprincipal (identiteit die door een toepassing wordt gebruikt) wilt krijgen, u de opdrachten [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) of [az ad sp-lijst](/cli/azure/ad/sp#az-ad-sp-list) gebruiken. Voor een serviceprincipal gebruikt u de object-id en **niet** de toepassings-id.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In RBAC voegt u een roltoewijzing toe om toegang te verlenen om toegang te verlenen.

### <a name="resource-group-without-parameters"></a>Resourcegroep (zonder parameters)

In de volgende sjabloon wordt een eenvoudige manier weergegeven om een roltoewijzing toe te voegen. Sommige waarden worden opgegeven in de sjabloon. De volgende sjabloon toont aan:

-  De [leesrol](built-in-roles.md#reader) toewijzen aan een gebruiker, groep of toepassing in een resourcegroepbereik

Als u de sjabloon wilt gebruiken, moet u het volgende doen:

- Een nieuw JSON-bestand maken en de sjabloon kopiëren
- Vervangen `<your-principal-id>` door de id van een gebruiker, groep of toepassing om de rol toe te wijzen aan

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Hier volgen voorbeelden [van new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [de az-groep implementatie maken](/cli/azure/group/deployment#az-group-deployment-create) opdrachten voor het starten van de implementatie in een resourcegroep met de naam ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

In het volgende ziet u een voorbeeld van de roltoewijzing reader aan een gebruiker voor een resourcegroep nadat de sjabloon is geïmplementeerd.

![Roltoewijzing op het bereik van resourcegroepen](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription"></a>Resourcegroep of -abonnement

De vorige sjabloon is niet erg flexibel. De volgende sjabloon gebruikt parameters en kan op verschillende scopes worden gebruikt. De volgende sjabloon toont aan:

- Een rol toewijzen aan een gebruiker, groep of toepassing in een resourcegroep of abonnementsbereik
- De rollen Eigenaar, bijdrager en lezer opgeven als parameter

Als u de sjabloon wilt gebruiken, moet u de volgende ingangen opgeven:

- De id van een gebruiker, groep of toepassing om de rol toe te wijzen aan
- Een unieke ID die wordt gebruikt voor de roltoewijzing, of u de standaard-id gebruiken

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Deze sjabloon is niet idempotent, tenzij dezelfde `roleNameGuid` waarde wordt opgegeven als parameter voor elke implementatie van de sjabloon. Als `roleNameGuid` er geen wordt opgegeven, wordt standaard een nieuwe GUID gegenereerd `Conflict: RoleAssignmentExists` op elke implementatie en mislukken volgende implementaties met een fout.

De omvang van de roltoewijzing wordt bepaald op basis van het niveau van de implementatie. Hier volgen voorbeelden [van new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [de az-groep implementatie maken](/cli/azure/group/deployment#az-group-deployment-create) opdrachten voor het starten van de implementatie op een resourcegroepbereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Hier volgen voorbeelden van nieuwe azDeployment en [az-implementatie](/powershell/module/az.resources/new-azdeployment) [maken](/cli/azure/deployment#az-deployment-create) opdrachten voor het starten van de implementatie op een abonnementsbereik en geven de locatie op.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource"></a>Resource

Als u een roltoewijzing op het niveau van een resource moet toevoegen, is de indeling van de roltoewijzing anders. U geeft de naamruimte van de resourceprovider en het resourcetype van de resource op om de rol toe te wijzen. U neemt ook de naam van de resource op in de naam van de roltoewijzing.

Gebruik de volgende indeling voor het type en de naam van de roltoewijzing:

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

De volgende sjabloon toont aan:

- Een nieuw opslagaccount maken
- Een rol toewijzen aan een gebruiker, groep of toepassing op het bereik van het opslagaccount
- De rollen Eigenaar, bijdrager en lezer opgeven als parameter

Als u de sjabloon wilt gebruiken, moet u de volgende ingangen opgeven:

- De id van een gebruiker, groep of toepassing om de rol toe te wijzen aan

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', guid(uniqueString(variables('storageName'))))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Als u de vorige sjabloon wilt implementeren, gebruikt u de opdrachten voor de brongroep. Hier volgen voorbeelden [van new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [de implementatie van az-groepen maken](/cli/azure/group/deployment#az-group-deployment-create) opdrachten voor het starten van de implementatie op een resourcebereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

In het volgende ziet u een voorbeeld van de toewijzing van de rol inzender aan een gebruiker voor een opslagaccount nadat de sjabloon is geïmplementeerd.

![Roltoewijzing op resourcebereik](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Nieuwe serviceprincipal

Als u een nieuwe serviceprincipal maakt en onmiddellijk probeert een rol toe te wijzen aan die serviceprincipal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een nieuwe beheerde identiteit maakt en vervolgens probeert een rol toe te wijzen aan die serviceprincipal in dezelfde Azure Resource Manager-sjabloon, kan de roltoewijzing mislukken. De reden voor deze fout is waarschijnlijk een replicatievertraging. De serviceprincipal wordt in één regio gemaakt; De roltoewijzing kan echter plaatsvinden in een andere regio die de serviceprincipal nog niet heeft gerepliceerd. Als u dit scenario wilt `principalType` aanpakken, moet u de eigenschap instellen bij `ServicePrincipal` het maken van de roltoewijzing.

De volgende sjabloon toont aan:

- Een nieuwe principal voor managed identity service maken
- Hoe geef je de`principalType`
- De rol Inzender toewijzen aan die serviceprincipal op een resourcegroepbereik

Als u de sjabloon wilt gebruiken, moet u de volgende ingangen opgeven:

- De basisnaam van de beheerde identiteit, of u de standaardtekenreeks gebruiken

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Hier volgen voorbeelden [van new-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) en [de az-groep implementatie maken](/cli/azure/group/deployment#az-group-deployment-create) opdrachten voor het starten van de implementatie op een resourcegroepbereik.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Hieronder ziet u een voorbeeld van de toewijzing van de rolinzender aan een nieuwe hoofdvoor beheerde identiteitsservice nadat de sjabloon is geïmplementeerd.

![Roltoewijzing voor een nieuwe hoofdvoor managed identity service](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Azure Resource Manager-sjablonen maken en implementeren via Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Informatie over de structuur en de syntaxis van Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md)
- [Resourcegroepen en resources maken op abonnementsniveau](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?term=rbac)
