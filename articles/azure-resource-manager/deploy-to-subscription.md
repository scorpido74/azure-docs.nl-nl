---
title: Abonnements niveau implementeren-Azure Resource Manager sjabloon
description: Hierin wordt beschreven hoe u een resource groep maakt in een Azure Resource Manager sjabloon. Ook wordt uitgelegd hoe u resources kunt implementeren in het bereik van Azure-abonnementen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: tomfitz
ms.openlocfilehash: 04a46700b68bcf498956f93c96ce2dccf1b555fe
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832717"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Resource groepen en-resources op abonnements niveau maken

Doorgaans implementeert u Azure-resources in een resource groep in uw Azure-abonnement. U kunt echter ook resources maken op abonnements niveau. U kunt implementaties op abonnements niveau gebruiken om acties uit te voeren die zinvol zijn op dat niveau, zoals het maken van resource groepen of [het toewijzen van toegangs beheer op basis van rollen](../role-based-access-control/overview.md).

Als u sjablonen wilt implementeren op abonnements niveau, gebruikt u Azure CLI, Power shell of REST API. De Azure Portal biedt geen ondersteuning voor implementatie in het abonnements niveau.

## <a name="supported-resources"></a>Ondersteunde resources

U kunt de volgende bron typen implementeren op abonnements niveau:

* [implementaties](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Het schema dat u voor implementaties op abonnements niveau gebruikt, wijkt af van het schema voor implementaties van resource groepen.

Voor sjablonen gebruikt u:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Gebruik voor parameter bestanden:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Implementatie opdrachten

De opdrachten voor implementaties op abonnements niveau verschillen van de opdrachten voor implementaties van resource groepen.

Gebruik [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)voor de Azure cli. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


Gebruik voor de Power shell-implementatie opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Gebruik voor REST API [implementaties-maken bij abonnements bereik](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op abonnements niveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy. json** implementeert, wordt er een standaard implementatie naam van **azuredeploy**gemaakt.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de fout code `InvalidDeploymentLocation`krijgt, moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloon functies gebruiken

Voor implementaties op abonnements niveau zijn er enkele belang rijke aandachtspunten bij het gebruik van sjabloon functies:

* De functie [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [resourceId ()](resource-group-template-functions-resource.md#resourceid) wordt ondersteund. Gebruik deze om de resource-ID op te halen voor resources die worden gebruikt bij implementaties op abonnements niveau. U kunt bijvoorbeeld de resource-ID voor een beleids definitie ophalen met `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`. Of gebruik de functie [subscriptionResourceId ()](resource-group-template-functions-resource.md#subscriptionresourceid) om de resource-id voor een resource op abonnements niveau op te halen.
* De functies [Reference ()](resource-group-template-functions-resource.md#reference) en [List ()](resource-group-template-functions-resource.md#list) worden ondersteund.

## <a name="create-resource-groups"></a>Resource groepen maken

Als u een resource groep in een Azure Resource Manager sjabloon wilt maken, definieert u een resource van het [micro soft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) met een naam en een locatie voor de resource groep. U kunt een resource groep maken en resources in dezelfde sjabloon implementeren voor die resource groep.

Met de volgende sjabloon maakt u een lege resource groep.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Gebruik het [element Copy](resource-group-create-multiple.md) met resource groepen om meer dan één resource groep te maken. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Zie [meer dan één exemplaar van een resource of eigenschap in azure Resource Manager sjablonen implementeren](./resource-group-create-multiple.md)en [zelf studie: meerdere resource-instanties maken met Resource Manager-sjablonen](./resource-manager-tutorial-create-multiple-instances.md)voor meer informatie over resource iteratie.

## <a name="resource-group-and-resources"></a>Resource groep en resources

Als u de resource groep wilt maken en resources hierop wilt implementeren, gebruikt u een geneste sjabloon. De geneste sjabloon definieert de resources die moeten worden geïmplementeerd in de resource groep. Stel de geneste sjabloon afhankelijk van de resource groep in om ervoor te zorgen dat de resource groep bestaat voordat u de resources implementeert.

In het volgende voor beeld wordt een resource groep gemaakt en wordt een opslag account geïmplementeerd in de resource groep.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Beleidsregels maken

### <a name="assign-policy"></a>Beleid toewijzen

In het volgende voor beeld wordt een bestaande beleids definitie toegewezen aan het abonnement. Als het beleid para meters accepteert, geeft u ze als een object. Als het beleid geen para meters heeft, gebruikt u het standaard lege object.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Als u deze sjabloon wilt implementeren met Azure CLI, gebruikt u:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Beleid definiëren en toewijzen

U kunt een beleid [definiëren](../governance/policy/concepts/definition-structure.md) en toewijzen in dezelfde sjabloon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Gebruik de volgende CLI-opdracht om de beleids definitie in uw abonnement te maken en deze toe te passen op het abonnement:

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Volgende stappen

* Zie [toegang tot Azure-resources beheren met RBAC en Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings. json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)(Engelstalig) voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* Voorbeeld sjablonen vindt u op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Zie [ontwerp sjablonen](resource-group-authoring-templates.md)voor meer informatie over het maken van Azure Resource Manager sjablonen. 
* Zie [sjabloon functies](resource-group-template-functions.md)voor een lijst met de beschik bare functies in een sjabloon.
