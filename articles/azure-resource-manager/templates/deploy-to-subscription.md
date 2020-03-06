---
title: Resources implementeren voor het abonnement
description: Hierin wordt beschreven hoe u een resource groep maakt in een Azure Resource Manager sjabloon. Ook wordt uitgelegd hoe u resources kunt implementeren in het bereik van Azure-abonnementen.
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: 2e747b7faa6e9766a577b472cc3e283d6223109e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379125"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Resource groepen en-resources op abonnements niveau maken

Doorgaans implementeert u Azure-resources in een resource groep in uw Azure-abonnement. U kunt echter ook resources maken op abonnements niveau. U kunt implementaties op abonnements niveau gebruiken om acties uit te voeren die zinvol zijn op dat niveau, zoals het maken van resource groepen of [het toewijzen van toegangs beheer op basis van rollen](../../role-based-access-control/overview.md).

Als u sjablonen wilt implementeren op abonnements niveau, gebruikt u Azure CLI, Power shell of REST API. De Azure Portal biedt geen ondersteuning voor implementatie in het abonnements niveau.

## <a name="supported-resources"></a>Ondersteunde resources

U kunt de volgende bron typen implementeren op abonnements niveau:

* [budgetten](/azure/templates/microsoft.consumption/budgets)
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


Gebruik voor de Power shell-implementatie opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) of **New-AzSubscriptionDeployment**. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
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

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden ondersteund.
* De functie [resourceId ()](template-functions-resource.md#resourceid) wordt ondersteund. Gebruik deze om de resource-ID op te halen voor resources die worden gebruikt bij implementaties op abonnements niveau. Geef geen waarde op voor de para meter van de resource groep.

  Als u bijvoorbeeld de resource-ID voor een beleids definitie wilt ophalen, gebruikt u:
  
  ```json
  resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  De geretourneerde Resource-ID heeft de volgende indeling:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

  Of gebruik de functie [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) om de resource-id voor een resource op abonnements niveau op te halen.

## <a name="create-resource-groups"></a>Resource groepen maken

Als u een resource groep in een Azure Resource Manager sjabloon wilt maken, definieert u een resource van het [micro soft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) met een naam en een locatie voor de resource groep. U kunt een resource groep maken en resources in dezelfde sjabloon implementeren voor die resource groep.

Met de volgende sjabloon maakt u een lege resource groep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Gebruik het [element Copy](copy-resources.md) met resource groepen om meer dan één resource groep te maken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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

Zie [meer dan één exemplaar van een resource in azure Resource Manager sjablonen implementeren](./copy-resources.md)en [zelf studie: meerdere resource-instanties maken met Resource Manager-sjablonen](./template-tutorial-create-multiple-instances.md)voor meer informatie over resource iteratie.

## <a name="resource-group-and-resources"></a>Resource groep en resources

Als u de resource groep wilt maken en resources hierop wilt implementeren, gebruikt u een geneste sjabloon. De geneste sjabloon definieert de resources die moeten worden geïmplementeerd in de resource groep. Stel de geneste sjabloon afhankelijk van de resource groep in om ervoor te zorgen dat de resource groep bestaat voordat u de resources implementeert.

In het volgende voor beeld wordt een resource groep gemaakt en wordt een opslag account geïmplementeerd in de resource groep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
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
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
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
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
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

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Beleid definiëren en toewijzen

U kunt een beleid [definiëren](../../governance/policy/concepts/definition-structure.md) en toewijzen in dezelfde sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
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
      "apiVersion": "2018-05-01",
      "name": "location-lock",
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
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>Voorbeelden van sjablonen

* [Maak een resource groep, vergrendel deze en geef er machtigingen voor](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Maak een resource groep, een beleid en een beleids toewijzing](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Volgende stappen

* Zie [toegang tot Azure-resources beheren met RBAC en Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings. json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)(Engelstalig) voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* Voorbeeld sjablonen vindt u op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).
* Zie [ontwerp sjablonen](template-syntax.md)voor meer informatie over het maken van Azure Resource Manager sjablonen.
* Zie [sjabloon functies](template-functions.md)voor een lijst met de beschik bare functies in een sjabloon.
