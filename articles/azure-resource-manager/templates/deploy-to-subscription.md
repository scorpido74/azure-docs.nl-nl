---
title: Resources implementeren voor een abonnement
description: Beschrijft hoe u een resourcegroep maakt in een Azure Resource Manager-sjabloon. Het laat ook zien hoe u resources implementeert op het Azure-abonnementsbereik.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 65cc220d32d1e1149b7026fc438f5e34262511dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131959"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Resourcegroepen en resources maken op abonnementsniveau

Als u het beheer van resources in uw Azure-abonnement wilt vereenvoudigen, u [beleidsregels](../../governance/policy/overview.md) of [op rollen gebaseerde toegangsbesturingselementen](../../role-based-access-control/overview.md) voor het hele abonnement definiëren en toewijzen. Met sjablonen op abonnementsniveau past u beleid aan en wijst u rollen toe aan het abonnement. U ook resourcegroepen maken en resources implementeren.

Als u sjablonen op abonnementsniveau wilt implementeren, gebruikt u Azure CLI, PowerShell of REST API. De Azure-portal biedt geen ondersteuning voor implementatie op abonnementsniveau.

## <a name="supported-resources"></a>Ondersteunde resources

U de volgende resourcetypen op abonnementsniveau implementeren:

* [Begrotingen](/azure/templates/microsoft.consumption/budgets)
* [implementaties](/azure/templates/microsoft.resources/deployments) - voor geneste sjablonen die worden geïmplementeerd in resourcegroepen.
* [eventAbonnementen](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [beleidToewijzingen](/azure/templates/microsoft.authorization/policyassignments)
* [beleidsdefinities](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinities](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstelacties](/azure/templates/microsoft.policyinsights/remediations)
* [resourceGroepen](/azure/templates/microsoft.resources/resourcegroups)
* [rolOpdrachten](/azure/templates/microsoft.authorization/roleassignments)
* [rolDefinities](/azure/templates/microsoft.authorization/roledefinitions)
* [scopeToewijzingen](/azure/templates/microsoft.managednetwork/scopeassignments)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tags](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Schema

Het schema dat u gebruikt voor implementaties op abonnementsniveau is anders dan het schema voor implementaties van resourcegroepen.

Gebruik voor sjablonen:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Het schema voor een parameterbestand is hetzelfde voor alle implementatiescopes. Gebruik voor parameterbestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Implementatieopdrachten

De opdrachten voor implementaties op abonnementsniveau zijn anders dan de opdrachten voor implementaties van resourcegroepen.

Gebruik voor Azure CLI [submaak az-implementatie.](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create) In het volgende voorbeeld wordt een sjabloon geïmplementeerd om een resourcegroep te maken:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Gebruik voor de opdracht [PowerShell-implementatie de optie Nieuwe-AzDeployment](/powershell/module/az.resources/new-azdeployment) of **Nieuwe-AzSubscriptionDeployment**. In het volgende voorbeeld wordt een sjabloon geïmplementeerd om een resourcegroep te maken:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Voor REST API gebruikt u [Implementaties - Maken bij abonnementsbereik](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van implementatie

Voor implementaties op abonnementsniveau moet u een locatie voor de implementatie opgeven. De locatie van de implementatie staat los van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen.

U een naam opgeven voor de implementatie of de standaardimplementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy.json** implementeert, wordt een standaardimplementatienaam van **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderlijk. U geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de `InvalidDeploymentLocation`foutcode krijgt, gebruikt u een andere naam of dezelfde locatie als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloonfuncties gebruiken

Voor implementaties op abonnementsniveau zijn er enkele belangrijke overwegingen bij het gebruik van sjabloonfuncties:

* De functie [resourceGroup()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De [functies referentie()](template-functions-resource.md#reference) en [list()](template-functions-resource.md#list) worden ondersteund.
* Gebruik de functie [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) om de resource-id op te halen voor resources die op abonnementsniveau zijn geïmplementeerd.

  Als u bijvoorbeeld de resource-id voor een beleidsdefinitie wilt opvragen, gebruikt u het als:
  
  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  De geretourneerde resource-id heeft de volgende indeling:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-resource-groups"></a>Resourcegroepen maken

Als u een resourcegroep wilt maken in een Azure Resource Manager-sjabloon, definieert u een [microsoft.resources/resourceGroups-bron](/azure/templates/microsoft.resources/allversions) met een naam en locatie voor de resourcegroep. U een resourcegroep maken en resources implementeren voor die resourcegroep in dezelfde sjabloon.

Met de volgende sjabloon wordt een lege resourcegroep gemaakt.

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

Gebruik het [kopieerelement](copy-resources.md) met resourcegroepen om meer dan één resourcegroep te maken.

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

Zie [Meer dan één instantie van een resource implementeren in Azure Resource Manager-sjablonen](./copy-resources.md)en [Zelfstudie: Meerdere resource-exemplaren maken met Resource Manager-sjablonen](./template-tutorial-create-multiple-instances.md).

## <a name="resource-group-and-resources"></a>Resourcegroep en resources

Als u de brongroep wilt maken en er resources op wilt implementeren, gebruikt u een geneste sjabloon. De geneste sjabloon definieert de resources die moeten worden geïmplementeerd in de resourcegroep. Stel de geneste sjabloon in als afhankelijk van de resourcegroep om te zorgen dat de resourcegroep bestaat voordat de resources worden geïmplementeerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt en wordt een opslagaccount geïmplementeerd in de resourcegroep.

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
              },
              "kind": "StorageV2"
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

In het volgende voorbeeld wordt een bestaande beleidsdefinitie aan het abonnement toegedeeld. Als het beleid parameters neemt, geeft u deze op als object. Als het beleid geen parameters aanneemt, gebruikt u het standaard lege object.

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

Gebruik het als volgt om deze sjabloon met Azure CLI te implementeren:

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Gebruik het als volgt om deze sjabloon met PowerShell te implementeren:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Beleid definiëren en toewijzen

U een beleid in dezelfde sjabloon [definiëren](../../governance/policy/concepts/definition-structure.md) en toewijzen.

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

Als u de beleidsdefinitie in uw abonnement wilt maken en deze op het abonnement wilt toepassen, gebruikt u de volgende opdracht CLI:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Gebruik het als volgt om deze sjabloon met PowerShell te implementeren:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="template-samples"></a>Voorbeelden van sjablonen

* [Maak een resourcegroep, vergrendel deze en geef er machtigingen aan](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).
* [Maak een resourcegroep, een beleid en een beleidstoewijzing](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Volgende stappen

* Zie [Toegang tot Azure-resources beheren met RBAC- en Azure Resource Manager-sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voorbeeld van het implementeren van werkruimte-instellingen voor Azure Security Center.
* Voorbeeldsjablonen zijn te vinden op [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments)
* U ook sjablonen implementeren op [managementgroepsniveau](deploy-to-management-group.md) en [tenantniveau.](deploy-to-tenant.md)
