---
title: Resources implementeren voor het abonnement
description: Hierin wordt beschreven hoe u een resource groep maakt in een Azure Resource Manager sjabloon. Ook wordt uitgelegd hoe u resources kunt implementeren in het bereik van Azure-abonnementen.
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 0673ea5260c7312395acde8a62b5d457657b9793
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729114"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Resource groepen en-resources op abonnements niveau maken

Om het beheer van resources te vereenvoudigen, kunt u een Azure Resource Manager sjabloon (ARM-sjabloon) gebruiken om resources te implementeren op het niveau van uw Azure-abonnement. U kunt bijvoorbeeld [beleid](../../governance/policy/overview.md) en [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) implementeren voor uw abonnement. Dit geldt voor het hele abonnement. U kunt ook resource groepen maken binnen het abonnement en resources implementeren voor resource groepen in het abonnement.

> [!NOTE]
> U kunt implementeren op 800 verschillende resource groepen in een implementatie op abonnements niveau.

Als u sjablonen wilt implementeren op abonnements niveau, gebruikt u Azure CLI, Power shell, REST API of de portal.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resource typen kunnen worden geïmplementeerd op het abonnements niveau. In deze sectie wordt een overzicht gegeven van de resource typen die worden ondersteund.

Gebruik voor Azure-blauw drukken:

* [artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blauw drukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versies (blauw drukken)](/azure/templates/microsoft.blueprint/blueprints/versions)

Gebruik voor Azure-beleid:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd op resource groepen, gebruikt u:

* [implementaties](/azure/templates/microsoft.resources/deployments)

Gebruik voor het maken van nieuwe resource groepen:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Gebruik voor het beheren van uw abonnement:

* [Advisor-configuraties](/azure/templates/microsoft.advisor/configurations)
* [budgetten](/azure/templates/microsoft.consumption/budgets)
* [Analyse profiel wijzigen](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Koptags](/azure/templates/microsoft.resources/tags)

Andere ondersteunde typen zijn:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Het schema dat u voor implementaties op abonnements niveau gebruikt, wijkt af van het schema voor implementaties van resource groepen.

Voor sjablonen gebruikt u:

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

Het schema voor een parameter bestand is hetzelfde voor alle implementatie bereiken. Gebruik voor parameter bestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Implementatie bereiken

Wanneer u naar een abonnement implementeert, kunt u een abonnement en alle resource groepen binnen het abonnement richten. U kunt niet implementeren in een abonnement dat verschilt van het doel abonnement. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Resources die zijn gedefinieerd in de sectie resources van de sjabloon, worden toegepast op het abonnement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Als u een resource groep in het abonnement wilt richten, voegt u een geneste implementatie toe en neemt u de `resourceGroup` eigenschap op. In het volgende voor beeld streeft de geneste implementatie naar een resource groep met de naam `rg2` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

In dit artikel vindt u sjablonen die laten zien hoe u resources implementeert in verschillende bereiken. Zie [resource groep en-resources maken](#create-resource-group-and-resources)voor een sjabloon waarmee u een resource groep maakt en een opslag account implementeert. Voor een sjabloon waarmee een resource groep wordt gemaakt, wordt er een vergren deling op toegepast en wordt er een rol toegewezen aan de resource groep. Zie [toegangs beheer](#access-control)voor meer informatie.

## <a name="deployment-commands"></a>Implementatie opdrachten

De opdrachten voor implementaties op abonnements niveau verschillen van de opdrachten voor implementaties van resource groepen.

Gebruik [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create)voor Azure cli. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Gebruik voor de Power shell-implementatie opdracht [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) of **New-AzSubscriptionDeployment**. In het volgende voor beeld wordt een sjabloon geïmplementeerd voor het maken van een resource groep:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

Gebruik voor REST API [implementaties-maken bij abonnements bereik](/rest/api/resources/deployments/createorupdateatsubscriptionscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op abonnements niveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy.jsop** implementeert, maakt de standaard implementatie naam **azuredeploy**.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de fout code krijgt `InvalidDeploymentLocation` , moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloon functies gebruiken

Voor implementaties op abonnements niveau zijn er enkele belang rijke aandachtspunten bij het gebruik van sjabloon functies:

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden ondersteund.
* Gebruik [resourceId ()](template-functions-resource.md#resourceid) niet om de resource-id op te halen voor resources die zijn geïmplementeerd op abonnements niveau. Gebruik in plaats daarvan de functie [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) .

  Als u bijvoorbeeld de resource-ID wilt ophalen voor een beleids definitie die is geïmplementeerd op een abonnement, gebruikt u:

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  De geretourneerde Resource-ID heeft de volgende indeling:

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>Resourcegroepen

### <a name="create-resource-groups"></a>Resource groepen maken

Als u een resource groep in een ARM-sjabloon wilt maken, definieert u een resource van het [micro soft. resources/resourceGroups](/azure/templates/microsoft.resources/allversions) met een naam en een locatie voor de resource groep.

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
      "apiVersion": "2020-06-01",
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
      "apiVersion": "2020-06-01",
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

### <a name="create-resource-group-and-resources"></a>Resource groep en-resources maken

Als u de resource groep wilt maken en resources hierop wilt implementeren, gebruikt u een geneste sjabloon. De geneste sjabloon definieert de resources die moeten worden geïmplementeerd in de resource groep. Stel de geneste sjabloon afhankelijk van de resource groep in om ervoor te zorgen dat de resource groep bestaat voordat u de resources implementeert. U kunt implementeren in Maxi maal 800 resource groepen.

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Beleids definitie toewijzen

In het volgende voor beeld wordt een bestaande beleids definitie toegewezen aan het abonnement. Als de beleids definitie para meters heeft, geeft u ze als een object op. Als de beleids definitie geen para meters heeft, gebruikt u het standaard lege object.

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
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
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
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Beleids definities maken en toewijzen

U kunt een beleids definitie [definiëren](../../governance/policy/concepts/definition-structure.md) en toewijzen in dezelfde sjabloon.

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
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Gebruik de volgende CLI-opdracht om de beleids definitie in uw abonnement te maken en toe te wijzen aan het abonnement:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprints

### <a name="create-blueprint-definition"></a>Definitie van blauw druk maken

U kunt een blauw druk-definitie [maken](../../governance/blueprints/tutorials/create-from-sample.md) op basis van een sjabloon.

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Als u de definitie van de blauw druk in uw abonnement wilt maken, gebruikt u de volgende CLI-opdracht:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Als u deze sjabloon wilt implementeren met Power shell, gebruikt u:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Toegangsbeheer

Zie [Azure-roltoewijzingen toevoegen met Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.

In het volgende voor beeld wordt een resource groep gemaakt, wordt er een vergren deling op toegepast en wordt een rol aan een principal toegewezen.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Volgende stappen

* Zie [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* Voorbeeld sjablonen vindt u op [github](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments).
* U kunt ook sjablonen implementeren op het niveau van de [beheer groep](deploy-to-management-group.md) en het [Tenant niveau](deploy-to-tenant.md).
